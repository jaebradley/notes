# [Using Atmoic Transactions To Power An Idempotent API](https://brandur.org/http-transactions)

## Simple User Creation Service

* `PUT` to `/users` with an `email` query parameter
* Inside request handler, check if user exist and return `201` if so, else, insert a new record for the user and insert a new "user action" record that will serve as audit log 
  * Audit log will have user id, action name, and timestamp
* In the request handler, create a transaction with serializiable isolation level

```sql
START TRANSACTION
    ISOLATION LEVEL SERIALIZABLE;

SELECT * FROM users
    WHERE email = 'jane@example.com';

INSERT INTO users (email)
    VALUES ('jane@example.com');

INSERT INTO user_actions (user_id, action)
    VALUES (1, 'created');

COMMIT;
```

* `email` needs a unique constraint because two transactions could be running concurrently where they both don't find a user with the email and they insert as part of two different transactions from two different requests
  * Luckily, serializable transactions emulates serial transaction execution as if each transaction had been executed one after the other

### Retrying an Abort

* Would be good to handle race condition properly vs. bubble up a `500`
* Retrying to create user would look something like

```ruby
MAX_ATTEMPTS = 2

put "/users/:email" do |email|
  MAX_ATTEMPTS.times do
    begin
      DB.transaction(isolation: :serializable) do
        ...
      end

      # Success! Leave the loop.
      break

    rescue Sequel::SerializationFailure
      log.error "Failed to commit serially: #{$!}"
      # Failure: fall through to the next loop.
    end
  end
end
```

### Background Jobs

* It's a common pattern to add jobs to a background queue during a request so that they can be worked out-of-band and a waiting client doesn't have to block on an expensive operation
  * If we used a common job queue like Sidekiq, then in the case of a transaction rollback, we could end up with an invalid job in queue - it will reference data that no longer exists so no matter how many times the job worker retries it, it will never succeed

### Transaction-staged Jobs

* Instead of ending up with "dead" jobs, create entries in a staging jobs table, and then an enqueuer pulls them out in batches and puts them in the job queue

```ruby
loop do
  DB.transaction do
    # pull jobs in large batches
    job_batch = StagedJobs.order('id').limit(1000)

    if job_batch.count > 0
      # insert each one into the real job queue
      job_batch.each do |job|
        Sidekiq.enqueue(job.job_name, *job.job_args)
      end

      # and in the same transaction remove these records
      StagedJobs.where('id <= ?', job_batch.last).delete
    end
  end
end
```

* Because jobs are inserted into the staging table from within a transaction, its isolation property (ACID’s “I”) guarantees that they’re not visible to any other transaction until after the inserting transaction commits so jobs are not executed early, even if the insert statement is part of another transaction (and has executed within that transaction). 
  * A staged job that’s rolled back (as part of another transaction) is never seen by the enqueuer, and doesn’t make it to the job queue.