# [How to set an ideal thread pool size](https://engineering.zalando.com/posts/2019/04/how-to-set-an-ideal-thread-pool-size.html)

* If a worker thread depends on a database, the thread pool is limited by the database's connection pool size (i.e. does it make any sense to have 1000 running threads in front of a database connection pool with 100 connections?)
  * If a worker thread calls an external service which can handle only a few requests simultaneously, the thread pool is limited by the throuput of this service as well
* Can get total number of CPUs via `Runtime.getRuntime().availableProcessors()`
* Number of threads = Number of Available Cores * (1 + Wait Time / Service Time)
  * The Wait TIme is the time spent waiting for I/O-bound tasks to complete (like waiting for an HTTP response form an external service)
  * Wait Time / Service Time is called the blocking coefficient
  * CPU-intensive tasks have a blocking coefficient close to 0, and thus the number of threads is equal to the number of available cores
* Example: worker threads calls microservice, serializes response to JSON and executes some logic
  * Microservice response time is 50ms and processing time is 5ms, so for a 2-core machine, `2 x (1 + 50 / 5) = 22` optimal thread size
  * However, have to consider HTTP connection pool, JDBC connection pool as well

## Little's Law

* The # of requests in a system equals the rate at which they arrive (Requests Per Second) x the average amount of time it takes to service an individual request (Latency)
  * So for a thread pool of `22` worker threads, and a response time of `55ms`, `22/55ms = 400 RPS`
