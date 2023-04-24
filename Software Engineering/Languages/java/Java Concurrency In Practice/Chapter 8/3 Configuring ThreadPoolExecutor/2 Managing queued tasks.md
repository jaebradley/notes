# 8.3.2 Managing queued tasks

* Bounded thread pools limit the number of tasks that can be executed concurrently (special case is single-threaded executors)
* For a fixed-sized thread pool, if the arrival rate of new requests exceeds the rate at which they can be handled, requests will still queue up
  * With a thread pool, these requests wait in a queue of `Runnables` managed by the `Executor` instead of queueing up as threads contending for the CPU
  * Representing a waiting task with a `Runnable` and a list node is certainly cheaper than with a thread
* Requests can arrive in bursts even when the average request rate is fairly stable
* `ThreadPoolExecutor` allows you to supply a `BlockingQueue` to hold tasks awaiting execution
  * 3 approaches - unbounded, bounded, and synchronous handoff
* Default for `newFixedThreadPool` and `newSingleThreadExecutor` is to use an unbounded `LinkedBlockingQueue`
  * Tasks will queue up if all worker threads are busy
* Bounded queues (`ArrayBlockingQueue`) help prevent resource exhuastion
  * Need to define a strategy for new tasks when the queue is full
  * Requires the queue size and pool size to be tuned together
  * For example, a large queue size, with a small thread pool size can reduce memory usage, CPU usage, and context switching, at the cost of potentially constraining throughput
