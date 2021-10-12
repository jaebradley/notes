# [Garbage Collection in Go: Part 1 - Semantics](https://www.ardanlabs.com/blog/2018/12/garbage-collection-in-go-part1-semantics.html)

* As of v1.12, Go uses a non-generational concurrent tri-color mark and sweep collector

## Collector Behavior

* Three phases of work for the collector
  * Two of the phases create "Stop The World" latencies and the other phase creates latencies that slow down the throughput of the application
  * Mark Setup (STW), Marking - Concurrent, Mark Termination (STW)

### Mark Setup

* First action when collection starts is to turn on the Write Barrier
* Write Barrier allows the collector to maintains data integrity on the heap during a collection since both the collector and application goroutine will be running concurrently
* To turn on the Write Barrier, every application goroutine running must be stopped
  * ~10-30 microseconds
* Collector must wait and watch for each goroutine to make a function call since function calls guarantee the goroutines are at a safe point to be stopped
* If a goroutine is in a tight loop performing math, then the goroutine will stall the collection from starting
* Goroutines *must* make function calls in reasonable timeframes

### Marking - Concurrent

* The collector takes 25% of the available CPU capacity for itself
* The collector uses Goroutines to do collection work - for the 4-threaded Go program in the article, one entire processor will be dedicated to collection work
* Marking consists of marking values in heap memory that are still in-use
  * Inspects the stacks for all existing goroutines to find root pointers to heap memory
  * Collector traverses heap memory graph from root pointers
  * While Marking is happening on one processor, application work can continue concurrently on other processors
  * Collector is now only impacting 25% of CPU capacity
  * If the collector determines it needs to slow down allocations (if the Marking work cannot be finished before the heap memory in-use reaches its limit) it sill recruit other application goroutines to assist with the marking work (Mark Assist)

### Mark Termination - STW

* The Write Barrier is turned off - all goroutines are stopped while the Mark Termination phase completes
* Takes ~60-90 microseconds

### Sweeping - Concurrent

* Sweeping is when the memory associated with values in heap memory that were not marked as in-use are reclaimed
* This activity occurs when application goroutines attempt to allocated new values in heap memory
* The latency of Sweeping is added to the cost of performing an allocation in heap memory and is not tied to any latencies associated with Garbage Collection

### Collector Latency Costs

* The amount of CPU capacity that must be dedicated to Marking and for Mark Assist
* Stop the World time is when no application goroutines are performing any of their application work and the application is effectively stopped
