# [Idempotency Is Easy Until the Second Request Is Different](https://blog.dochia.dev/blog/idempotency/)
* What if the second request arrives while the first request is still executing?
* What if the first request created a local payment, but crashed before publishing an event?
* What if the first request called a payment provider, and the provider accepted the payment, but the process died before recording the result?
* What if the second request has the same idempotency key, but a different amount?
* Author’s opinion is that the same idempotency key + different canonical command should result in a hard error
  * A client that believes it is safely retrying a payment request should not result in a server silently interpreting the second request as something else

## Idempotency is about the effect
* An operation is idempotent if applying it once or many times has the same intended effect
* A uniqueness constraint can prevent one class of duplication, but does not, by itself, give the client a correct retry result
  * For example, `unique(account_id, merchant_id)` might prevent two identical rows, but if the client receives a `500` response code, the client does not know if the payment succeeded
  * If the row exists, but the response is different, or the event is published twice, or the ledger entry is duplicated, the operation is not idempotent in the way the caller cares about

## What you need to remember
* For `POST /payments` the durable idempotency record needs to answer
  * Who owns this key?
  * What did the first command mean
  * What outcome can be replayed
```
create table idempotency_requests
(
    tenant_id       text        not null,
    operation_name  text        not null,
    idempotency_key text        not null,
    request_hash    text        not null,
    status          text        not null,
    response_status int,
    response_body   jsonb,
    resource_type   text,
    resource_id     text,
    error_code      text,
    created_at      timestamptz not null,
    updated_at      timestamptz not null,
    expires_at      timestamptz not null,
    locked_until    timestamptz,
    primary key (tenant_id, operation_name, idempotency_key)
);
```
* Idempotency key should not be globally unique since a broken client generating some key should only collide with itself, not with another tenant
* `request_hash` is the server’s memory of the first command
  * Same idempotency key + different body becomes ambiguous as the first response is replayed for a different command, or a new operation is executed for the same key
  * Both of these outcomes are bad for a client that thinks it is retrying
* A retry can arrive while the first request owns execution
* Response fields exist because idempotency is not just about preventing duplicate writes but also providing the client with identical response(s)
```
Existing record | Same canonical command | Suggested behavior
none	yes	insert IN_PROGRESS and execute
COMPLETED	yes	replay stored response or documented equivalent
any existing record	no	reject with idempotency conflict
IN_PROGRESS, fresh	yes	wait, return 202, or return 409 + Retry-After
IN_PROGRESS, stale	yes	recover ownership; do not blindly execute again
FAILED_REPLAYABLE	yes	replay stored failure
FAILED_RETRYABLE	yes	allow retry according to policy
UNKNOWN_REQUIRES_RECOVERY	yes	trigger reconciliation or return pending/recovery status
expired/deleted	unknown	follow documented expiry behavior
```

## Same key, different command
* Two requests with identical idempotency keys, but different amounts
* Should *not* return the first response for the second request - if the caller does not introspect the response carefully, it may believe that the second request was successful
* Author makes argument that this behavior is *not* idempotency but rather reinterpretation
* Instead, this scenario should result in a `409 CONFLICT` regardless of if the first operation completed, failed, or is still executing

## Hash the command, not the bytes
* Two bodies that contain the same data, but in different order represent the same “command”
* However, this becomes murky when considering server defaults - if `fieldA` defaults to `valueB` are two response bodies where `fieldA` is set explicitly to `valueB` vs. not set at all the same?
* Proposed reasonable flow
```
1. Parse the request into a versioned request DTO or command.
2. Normalize values your API treats as equivalent: amounts, enum casing, default fields, timestamp precision.
3. Exclude transport-only metadata.
4. Include path parameters and operation name.
5. Include semantic headers if they affect the operation, such as API version.
6. If a header only affects response shape, such as Prefer: return=minimal, decide whether it belongs in the command hash, the replay contract, or neither.
7. Exclude Authorization and the idempotency key itself.
8. Serialize canonically.
9. Hash with a stable algorithm.
```

## The first insert decides who owns execution
* Case is two identical requests with the same command and tenant
```
if rows_inserted == 1:
    this request owns execution
else:
    existing = load idempotency row

    if existing.request_hash != request_hash:
        return 409 IDEMPOTENCY_KEY_REUSED_WITH_DIFFERENT_REQUEST

    if existing.status == COMPLETED:
        return replay(existing.response_status, existing.response_body)

    if existing.status == IN_PROGRESS and existing.locked_until > now():
        return 202 or 409 + Retry-After

    if existing.status == IN_PROGRESS and existing.locked_until <= now():
        attempt recovery ownership
        # this must be atomic too

    if existing.status == UNKNOWN_REQUIRES_RECOVERY:
        trigger reconciliation or return pending/recove
```
* Often times, a single database transaction that covers the idempotency row, the business payment row, and the outbox row is the simple initial design
```
begin transaction

insert idempotency row as IN_PROGRESS
insert payment row pay_789
insert outbox event PaymentCreated(pay_789)
update idempotency row:
  status = COMPLETED
  resource_type = payment
  resource_id = pay_789
  response_status = 201
  response_body = {...}

commit
```

* However, external side effects, like calling a payment provider, change this implementation strategy
  * Should not call provider while holding a database transaction, for example
  * Committing before the provider call means database state may say “in progress” while side-effect execution continues outside the transaction
  * If side-effect process crashes, then there needs to be some type of recovery
* Redis `SET NX EX` is proposed as a solution
  * However, it is not durable memory of the operation outcome
  * If the Redis lock expires while the payment provider call is executing, another request can enter
  * If the payment provider side-effect process dies after the provider call succeeds but before storing the response, the lock does not help any retry mechanism understand what happened

## The provider timeout is where your guarantee ends
* If the payment provider receives the request and succeeds, but their API errors in some way (time out, server crash, etc) and your process dies before recording this API error, then your database cannot infer whether money moved
* When the client retries with the same key, the retry behavior depends on the database state
  * If the state associated with the same key is `COMPLETED`, replay the original response
  * If the retry logic sees a fresh `PROVIDER_REQUEST_SENT` state, return `202 Accepted`, `409 Conflict` with a `Retry-After` header, or block briefly and wait for completion
    * Clients need to know whether to retry, poll, or wait
