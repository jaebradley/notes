# [Why does one NGINX worker take all the load?](https://blog.cloudflare.com/the-sad-state-of-linux-socket-balancing/)

* 3 ways of designing a TCP server with regards to performance
  * Single listen socket, single worker process
    * Processing limited to a single CPU
    * Single worker process is doing both accept() calls to receive new connections and processing the requests themselves
    * Single accept queue
  * Single listen socket, multiple worker processes
    * New connections exist in a single kernel data structure (the listen socket)
    * Multiple worker processes do both the acceppt() calls and process the request
    * Single accept queue
  * Multiple worker processes, each with separate listen socket
    * Using the SO_REUSEPORT socket option, can create a dedicated kernel data structure (the listen socket) for each worker process
    * Avoids listen socket contention
    * Multiple accept queues - once for each worker

## Spreading the accept() load

* Blocking accept (i.e. `sd.setBlocking` is not set to `False`)
 * Share the accept queue across multiple processes
* epoll and accept (i.e. `sd.setBlocking` is set to `False`)
 * Have a dedicated epoll in each worker process
 * Worker calls non-blocking accept only when epoll reports new connections
 * Avoid thundering-herd issue by using the `EPOLLEXCLUSIVE` flag
* Blocking accept distributes connectiosn across all workers (if `6` connections and `3` workers, each will get `2` connections)
* epoll and accept uses LIFO bheavior where the process added to the waiting queue most recently will get the new connection
 * Thus, the busiest process receives the majority of the new connections
* The `SO_REUSEPORT` socket option spreads incoming connections using simple hashing

## Waitroose vs Tesco Superstore

* A single clogged cashier will not significantly affect the latency of the whole system (Waitrose)
 * The reaminder of the load will be spread across the less busy cashiers
 * The shared queue will be drained promptly
* A separate queue for each cashier (Tesco) will suffer from large latency issues if a single cashier gets blocked as all traffic waiting in its queue will stall
 * Thus the maximum latency will grow if any single queue gets stuck
* In the test cases, a single accept queue (while not balancing load evenly) had much better latency values that were predictable
* With a `SO_REUSEPORT` multi-queue NGINX setup, the max value significantly increased with a large standard deviation
