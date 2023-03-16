# 8.3 Configuring `ThreadPoolExecutor`

## 8.3.1 Thread creation and teardown

* The implementation attempts to maintain the thread pool at the core pool size even when there are no tasks to execute
  * Will not create more threads than this unless the work queue is full
* Maximum pool size is the upper bound on how many pool threads can be active at once
* A thread that has been idle for longer than the keep-alive time becomes a candidate for reaping and can be terminated if the current pool size exceeds the core size
* Reaping idle threads incurs additional latency due to thread creation if threads must later be created when demand increases
* `newFixedThreadPool` sets the core and maximum pool size to the requested pool size, creating the effect of infinite timeout
* `newCachedThreadPool` sets the maximum pool size to `Integer.MAX_VALUE` and the core pool size to zero, with a timeout of 1 minute
  * This creates the effect of an infinitely expandable thread pool that will contract when demand decreases (i.e. threads are idle)
