# [Scaling Postgres `LISTEN`/`NOTIFY`](https://www.dbos.dev/blog/postgres-listen-notify-scalability)


## [HackerNews Discussion](https://news.ycombinator.com/item?id=49040296)
* [Commenter](https://news.ycombinator.com/item?id=49040671) argues that premature optimization should be replace with "using technology that has the wrong scaling factors"
  * Super scalable technology, and the complications these systems can introduce, can be just as bad a choice as introducing unscalable technology
  * For the commenter, a `LISTEN`/`NOTIFY` ceiling of 60K operations per second is small enough - the commenter prefers to have at least an order of magnitude of free capacity available, if possible
* Another [commenter](https://news.ycombinator.com/item?id=49043376) mentions how there was a queueing system built using `LISTEN`/`NOTIFY` due to the consistency guarantees associated with `LISTEN`/`NOTIFY` and the fact that this functionality was baked into Postgres
  * There was disk contention on the RDS instance, and vacuuming was non-performant
  * Another [commenter](https://news.ycombinator.com/item?id=49043491) points out that building a queueing system that is independent of other architectural pieces has advantages
