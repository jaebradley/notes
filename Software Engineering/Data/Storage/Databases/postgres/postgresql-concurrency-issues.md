# [PostgreSQL Concurrency Issues](https://www.postgresql.org/files/developer/concurrency.pdf)

* Transactions should appear to occur serially (i.e. one at a time, in isolation)
* Even when two transactions occur at the same time there shouldn't be any behavior that reveals they are concurrent
* Postgres uses a snapshot-based approach for implementing concurrency
  * Transactions run against some snapshot of the **committed** state
  * Transactions do not visibility into rows that have not yet been committted or rows that were committed after the snapshot
* Postgres has two isolation levels
  * `SERIALIZABLE` - snapshot is taken at the start of first query within transaction
  * `READ COMMITTED` - snapshot is taken at start of each query (so view of state can change throughout transaction)
    * So readers would be able to see new committed rows from writer beginning at the first SQL statement _after_ the writer has committed

## Concurrent Writing

* Two concurrent transactions cannot write the same row
* One transaction has to wait for the other to commit or abort
* If the first transaction aborts, the second transaction will proceed using non-updated version of row
* If the first transaction succeeds, the second transaction will
  * abort in `SERIALIZABLE` mode
  * use the newest version of the row in `READ COMMITTED` mode
    * will recheck new row to see if it satisfies `WHERE` clause of query
* Issue with a `READ COMMITTED` transaction with `SELECT` and then an `UPDATE`

```psql
BEGIN;
SELECT hits FROM webpages WHERE url = 'blah';
# Calculate $newval as $hits + 1
UPDATE webpages SET hits = $newval WHERE url = 'blah';
COMMIT;
```

* Imagine a case where two transactions start executing at the same time
* They both calculate the same `$newval`
* One transaction commits
* The other transaction will recheck that row exists (it does, it just has the same value)
* Then the other transaction will commit but won't increment appropriately
* Can use `SELECT FOR UPDATE`
  * `FOR UPDATE` causes the `SELECT` to acquire a row lock on the target row
  * In previous case of two concurrent transactions, second transaction will need to wait until first transaction has committed before being able to query the data

### Deadlock

* One transaction wants to move money from Alice to Bob
* Another transaction wants to move money from Bob to Alice
* Deadlock could occur where first transaction locks Alice's row, second transaction locks Bob's row, and then when they try to reference the other person's row those are locked
* Postgres will detect the deadlock and abort one of the two transactions

### Multi-row Constraints

* Check to see if we always have $1000 in checking and savings account

```psql
BEGIN;

UPDATE my_accounts SET balance = balance - $withdrawal_amount WHERE account_id = 'checking';
SELECT SUM(balance) FROM my_accounts;


if (sum >= 1000.0)
  COMMIT;
else
  ROLLBACK;
```

* Could end up moving too much money
  * Consider $600 dollars in checking and savings accounts
  * Two transactions start at the same time
  * First transaction withdraws $200 from checking and updates the balance to $400
  * Second transaction withdraws $200 from savings and updates the balance to $400
  * First transaction sums balance and gets $1000 (note that neither transaction has committed yet)
  * Second transaction sums balance and gets $1000
  * First transaction commits
  * Second transaction commits
  * Total is now $800
* Postgres does not do _predicate locking_ which is locking all rows that satisfy `WHERE` condition as it's very expensive
* Only solution is to lock table 

