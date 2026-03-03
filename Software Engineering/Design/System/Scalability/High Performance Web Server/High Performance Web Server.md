# [High Performance Web Server](https://gist.github.com/ankithooda/6f3a7fd65996c3023bc9057316d9d335)

## Data Copies
* An example of a data copy that is disguised rather than hidden is a hash function
  * Hash functions have the memory-access cost of a copy, while also involving more computation
* Pass buffer descriptors around instead of buffer pointers to avoid data copies
* Each buffer descriptor consists of
  * Pointer and length of the whole buffer
  * Pointer and length of the buffer that's actually filled
  * Forward / backward pointers to other buffer descriptors in the list
  * Reference count
* The reference count is used to ensure identify when the buffer descriptor is used
* Identify the largest objects in a program and make sure those get allocated separately so that they don't need to be copied
* Don't avoid a data copy by doing something worse, like forcing a context switch or breaking up large I/O requests

## Context Switches
* From the author's anectdotal experience, context switches are behind decreased performance at high load than data copies
* System starts spending more time going from one thread to another than it actually spends within any one thread doing useful work
* The #1 cause of context switches is having more active threads than you have processors
  * As the ratio of active threads to processors increases, the number of context switches can also increase exponentially
* Multi-threaded designs that have one thread per connection scale very poorly
* One alternative for a scalable system is to limit the number of active threads so that its less than or equal to the number of processors
* The first thing that a thread-frugal program has to do is figure out how it is going to make one thread handle multiple connections at once
  * Usually involves an event-driven architecture that uses select/poll, asynchronous I/O, or signals
* The simplest conceptual model of a multi-threaded event-driven server has a queue at its center
  * Requests are read by one or more listener threads and put on queues
  * One or more worker threads will remove these requests and process them
  * The #2 cause of context switches is transferring work from one thread to another
  * And if the response to a request is sent by the listener thread, then there will be at least two context switches
* Usually, it's not possible to know how many threads will be active in the future
  * Background threads could become active in the next instant
  * If you don't know how many threads are active, use a counting semaphore which each thread must hold whenever it is doing "real work"
  * If the thread limit has been achieved, then each listening thread might incur one extra context switch when it wakes up and then blocks on the semaphore
    * Once all listening threads are blocked, they won't continue contending for the resource until one of the existing threads retires
* Each stage when processing a request needs to be able to specify "what should happen next" for a given request
  * The request needs to be passed on to another stage via an ID or pointer in the return value
  * The request has been completed
  * The request is blocked
    * Similar to a completed request except that the request is not freed and will continue later from another thread
  * Queueing of requests is *not* done *between* stages
    * Avoids putting a request on a successor stage's queue, only to immediately invoke this stage and dequeue the same request

## Memory Allocation
* Preallocating everything a request handler might need at the beginning of the request handling logic might be preferable to allocating each piece as it is needed
  * Preallocation might not be feasible when memory is very tight
* Use lookaside lists for objects that are allocated and freed frequently
  * Put recently-freed objects onto a list instead of actually freeing them
  * If they are needed again, they need to be taken off the list instead of allocated from system memory
    * Can skip complex object initialization
  * Usually necessary to have a periodic sweeper task to free inactive objects, but also undesirable if the sweeper locking complexity and contention
  * Lookaside list consists of two lists, an "old" and a "new" list where both have different locks
  * Memory is allocated from the new list if possible, then from the old list, and only from system memory, as a last resort
    * New objects are always freed onto the new list
  * Sweeper thread operates
    * Lock both lists
    * Save the head for the old list
    * Make the previous new list into the "old" list by assigning list heads
    * Unlock both lists
    * Free everything on the saved old list
    * Objects are only actually freed when they have not been needed for at least one full sweep, but always less than two
  * Sweeper can do most of its work without holding any locks to contend with regular threads
* Could create a separate lookaside list for each thread to avoid any possibility of contention between threads

## Lock Contention
* Mental map of locking within a program
  * Vertical axis represents code
  * Horizontal axis represents data
    * In every stage, each request should be assigned to a data set with its own resources that are separate from any other data set
  * The grid formed by these two axes contains cells, where each cell represents a particular data set in a particular processing stage
  * Two requests should not be in contention unless they are in the same data set and the same processing stage i.e. they share a cell
* It is often helpful to make sure that data-set assignment is different for each stage so that requests that would contend at one stage are guaranteed not to do so at another stage
