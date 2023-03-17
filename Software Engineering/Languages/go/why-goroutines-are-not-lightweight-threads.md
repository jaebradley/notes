# [Why Goroutines Are Not Lightweight Threads?](https://codeburst.io/why-goroutines-are-not-lightweight-threads-7c460c1f155f)

* A thread is just a sequence of instructions that can be executed independently by a processor
* In the case of a webserver, a thread can be created (or taken from a thread pool) and requests can be delegated to these threads to achieve concurrency
* Threads are theoretically lighter than processes
  * Threads share memory and don't need to create new virtual memory space when they are created and thus don't require a memory management unit context switch
  * Communication between threads is simpler since they have shared memory while processes require various modes of Inter-Process Communications like semaphores, message queues, pipes, etc.
* Threads consume a lot of memory due to their large stack size (>=1MB), so 1000s of threads = GBs of memory
* Threads need to store a fair number of registers which hurts application performance
* Thread setup and teardown requires calls to the operating system for resources, which is slow

## Goroutines

* Goroutines exist only in the virtual space of the Go runtime and not in the operating system
* There is a Go Runtime scheduler that manages the lifecycle of these Goroutines
* The Go Runtime maintains three C structs
  * G struct - represents a single Goroutine with it's properties like stack pointer, ID, cache, etc
  * M struct - represents an OS thread, that contains a pointer to the global queue of runnable Goroutines, the current running Goroutine, and a reference to the scheduler
  * Scheduler struct - global struct that contains what queues are free and the waiting goroutines
* A Goroutine is created with initially only 2KB of stack size
* If a Goroutine blocks on system call, it blocks the OS thread that is executing the Goroutine
  * Another thread is taken from the waiting queue in the Scheduler struct and used for other runnable Goroutines
* If communication is done via Go channels, the OS doesn't block the thread
  * The Goroutine goes into a waiting state and other runnable Goroutines from the M struct are scheduled in it's place
* Another goroutine will be scheduled if the current one is blocking or done
  * Channel send / receive operations
  * Blocking system calls like file and network operations
  * After being stopped for a garbage collection cycle
