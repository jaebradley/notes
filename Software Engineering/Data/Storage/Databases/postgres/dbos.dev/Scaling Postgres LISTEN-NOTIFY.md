# [Scaling Postgres `LISTEN`/`NOTIFY`](https://www.dbos.dev/blog/postgres-listen-notify-scalability)

## The `LISTEN`/`NOTIFY` Exclusive Lock
* Commiting a transaction that calls `NOTIFY` requires acquiring a global exclusive lock
* Ownership of this lock is taken as the transaction begins to commit
  * Ownership is not released until the transaction is fully committed and persisted to disk
* This lock is necessary because Postgres guarantees that notifications are sent in transaction commit order
* All outgoing notifications are stored in a global internal queue and notifications that are added to this queue as part of the commit
* Postgres doesn't assign transactions a commit order until the transaction are finished committing as a commit can take varying amounts of time
* However, there is an ordering problem because transactions don't know their order until _after_ they are committed but also these transactions must be added to the queue in commit order
* The global lock serializes commits of transactions containing notificaations so that these transactions have a commit order that is defined ahead of time
* Because each write needs to acquire this global lock and hold it for the duration of the commit, this means that Postgres's optimizations, like grouped commits that are all flushed to disk via a single `fsync` call don't occur
* This bottleneck may not show up in resource consumption, like CPU or disk, as these writes-with-`NOTIFY` cannot complete faster than Postgres can commit transactions

## Optimizing `LISTEN`/`NOTIFY`
* For many applications of `LISTEN`/`NOTIFY`, the actualy value in the message isn't the source of truth
  * The application logic looks something like "when a notification is received, read from the table"
* Thus, the notifications don't have to be perfectly ordered nor durable and instead, can be buffered in memory and then periodically flushed to disk in a single batch transaction
* If a process crashes while notifications have been buffered but not flushed, these notifications will never be delivered
  * Stream readers don't just wait for notifications, they also periodically poll the database to check if the stream was written to without a notification
  * Authors chose a low frequency, because this mechanism is used strictly as a fallback, as to not significantly affect performance

## [HackerNews Discussion](https://news.ycombinator.com/item?id=49040296)
* [Commenter](https://news.ycombinator.com/item?id=49040671) argues that premature optimization should be replace with "using technology that has the wrong scaling factors"
  * Super scalable technology, and the complications these systems can introduce, can be just as bad a choice as introducing unscalable technology
  * For the commenter, a `LISTEN`/`NOTIFY` ceiling of 60K operations per second is small enough - the commenter prefers to have at least an order of magnitude of free capacity available, if possible
* Another [commenter](https://news.ycombinator.com/item?id=49043376) mentions how there was a queueing system built using `LISTEN`/`NOTIFY` due to the consistency guarantees associated with `LISTEN`/`NOTIFY` and the fact that this functionality was baked into Postgres
  * There was disk contention on the RDS instance, and vacuuming was non-performant
  * Another [commenter](https://news.ycombinator.com/item?id=49043491) points out that building a queueing system that is independent of other architectural pieces has advantages
