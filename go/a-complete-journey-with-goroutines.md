# [A Complete Journey With Goroutines](https://medium.com/@riteeksrivastava/a-complete-journey-with-goroutines-8472630c7f5c)

* The Go runtime will also split work across multiple processors (if there are other processors that are free) so there might be _some_ parallelism but mostly the benefit is concurrency (executing two tasks at the same time, but not necessarily _simultaneously_)
* If task A is blocked on something like waiting for I/O, the scheduler knows that the task is blocked and will check to see if there's another Goroutine that's ready to run while waiting for I/O to finish
* Goroutines require much less memory than threads (2kb vs. 1MB)
  * Goroutine stack sizes should grow and shrink according to the application
* OS thread setup and teardown is relatively expensive as Goroutine creation / destruction is handled by the Go runtime
* OS threads are scheduled preemptively meaning that if a process is taking too long, the scheduler would schedule the execution of another runnable process
  * Because of this, the scheduler needs to save more registers and data about the other runanble processes
  * Goroutines are scheduled cooperatively and do not talk directly to the operating system - when Goroutines are switched, something like 3 registers need to be saved

## Cooperative Scheduling

* No concept of scheduler time - Goroutines only yield control when idle or logically blocked
  * CHannels send and receive operations, if those are blocking operations
  * Blocking system calls like file and network operations
  * Garbage collection cycle
* The Go scheduler distributes runnable Goroutines over multiple OS threads that run one or more processors
* Each processor has a Goroutine queue, and every OS thread is assigned to a processor
  * There are at most `GOMAXPROCS` number of processors
