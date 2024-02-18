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
* Large / unbounded pools can utilize `SynchronousQueue`
  * Not a queue but a mechanism for managing hadoffs between threads
  * To put an element on a `SynchronousQueue`, another thread must be waiting to accept the handoff
  * If no thread is waiting, but the current pool size is less than the maximum, `ThreadPoolExecutor` creates a new thread
    * Otherwise, the task is rejected according to the saturation policy
  * More efficient than placing the task on a queue and having the worker thread fetch it from the queue
* Bounding either the thread pool or the work queue is suitable only whent asks are independent
  * With tasks that depend on other tasks, bounded thread pools can cause thread starvation deadlock so an unbounded pool configuration like `newCachedThreadPool` should be used
