# [Scheduling In Go: Part 2](https://www.ardanlabs.com/blog/2018/08/scheduling-in-go-part2.html)

* When a Go program starts, it's given a processor for every virtual core identified on the machine
  * MacBook Pro has a single processor with four physical cores
  * Intel Core i7 processor has hyper-threading which means there are two hardware threads per physical core - Go program will think there's eight virtual cores available
    * `runtime.NumCPU()` will return `8`
* Every processor is assigned an OS thread - thus, for every Go program  there are 8 threads available to do work
* Each processor is given a local run queue that manages the Goroutines assigned for execution in that thread
* Goroutines take turns being context-switching on and off the thread assigned to the processor
* There is a global run queue that is for Goroutines that have not been assigned to a processor yet
* OS scheduler is preemptive scheduler which means that the scheduler is making non-deterministic decisions
* Goroutine states
  * Waiting - Goroutine is waiting for operating system or synchronization event (atomix / mutex operations)
  * Runnable - Goroutine wants time on a thread so it can execute - individual time of any given Goroutine gets shorter the more Goroutines are competing for time
  * Executing - Goroutine has been placed on a thread and is executing instructions
* There are four types of events that allow scheduler to make schedulng decision
  * Keyword `go`
    * Once a new Goroutine is created, it gives scheduler opportunity to make scheduling decision
  * Garbage collection
    * GC uses own set of Goroutines and these Goroutines need a thread to run on
    * Scheduler is built to optimize around GC - like context-switching a Goroutine that wants to touch the heap with those that don't touch the heap during GC
  * System calls
    * If Goroutine is making a system call that causes Goroutine to block the thread, sometimes the scheduler is capable of context-switching the Goroutine off the thread and context-switching a new Goroutine onto the same thread
  * Synchronization and orchestration
    * If an atomic or mutex call causes a Goroutine to block, the scheduler can context-switch a new Goroutine to run
    * If the blocked Goroutine can run again, it can be re-queued and eventually context-switch back on an thread
* Processing asynchronous network-based systems
  * Network poller allows scheduler to prevent Goroutines that are waiting for a network response to block a thread
  * So case where Goroutine A wants to execute a network system call a thread while there are three more Goroutines waiting on the local run queue
  * Goroutine A is moved to network poller and network call is made - thread is now available to execute next Goroutine on local run queue
  * Network system call is completed by network poller and Goroutine A is moved back into local run queue to be context-switched back onto the thread at some point
  * So to execute network system calls, no extra threads are needed - the network poller has an OS thread
* If a processor finishes all it's work (i.e. it's local run queue is empty) it will first try and steal work from other processors
  * If a processor has work, it will take half of it's Goroutines
  * If no other processors have anything in their local run queues, check the global run queue
  * This is called "work-stealing" and allows threads to stay busy and not go idle
* The benefit of Goroutines and it's design vs. standard threading in C, for example in the basic example articles give of threads passing messages back and forth
  * In C, threads will be continuously context-switched off cores which means many instructions and thus, latency
  * In Go, the scheduler can use a single thread and processor to have the same behavior of threads passing messages back and forth
    * So Goroutine A would send a message and wait for a response
    * While it's waiting, it gets context-switched off the thread and moved into a waiting state
    * Goroutine B sees the message and moves to a runnable state - scheduler context switches it onto a thread (say the same thread as Goroutine A) and Goroutine B processes that message and sends a message back to Goroutine A
    * The same OS thread and Core is being used for all the work
    * From the standpoint of the OS, the OS thread never moves into a waiting state