# [Transactionally Staged Job Drains In Postgres](https://brandur.org/job-drain)

* Workflow that starts transaction, executes a few DB operations and queues a job has to consider if worker starts running before enclosing transaction commits

```ruby
DB.transaction do |t|
  db_op1(t)
  queue_job()
  db_op2(t)
end
```

* Also problematic is if / when transaction is rolled back - in this case, data is discarded completely and jobs inserted into the queue that were dependent on certain data being committed will never succeed
* If job is queued after transaction is committed, risk that program crashes after commit but before job makes it to the queue - maybe be hard to notice this happen

## Transactions As Gates

* Jobs are staged in a table within the database itself
* An enqueuer process reads the table and sends any jobs it finds to job queue before removing rows

```ruby
# Only one enqueuer should be running at any given time.
acquire_lock(:enqueuer) do

  loop do
    # Need at least repeatable read isolation level so that our DELETE after
    # enqueueing will see the same jobs as the original SELECT.
    DB.transaction(isolation_level: :repeatable_read) do
      jobs = StagedJob.order(:id).limit(BATCH_SIZE)

      unless jobs.empty?
        jobs.each do |job|
          Sidekiq.enqueue(job.job_name, *job.job_args)
        end

        StagedJob.where(Sequel.lit("id <= ?", jobs.last.id)).delete
      end
    end

    # If `staged_jobs` was empty, sleep for some time so
    # we're not continuously hammering the database with
    # no-ops.
    sleep_with_exponential_backoff
  end

end
```

* Transactional isolation means that the enqueuer is unable to see jobs that aren't yet committed (even if they've been inserted by an uncommitted transaction)
* If a job record is inserted within a transaction but then the transaction is rolled back, the job is discarded
* Enqueuer is resistant to job loss - jobs are removed only after they've been successfully enqueued to Sidekiq
  * If worker dies, enqueuer will pick back up from beginning and send along any jobs it missed
  * At least once delivery is guaranteed (though it may be more)