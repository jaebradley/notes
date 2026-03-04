# [Steps in Context Switching]([https://stackoverflow.com/a/40285741](https://stackoverflow.com/a/7443719))
* Operating system schedules threads, not processes
  * Threads are the only executable units in the operating system
  * Process switch is a thread switch where the threads belong to different processes
* There are involuntary, voluntary, and semi-voluntary switches
  * Involuntary switch occurs where some external event that impacts scheduling occurs outside the currently running thread
    * Example: An expired timer has woken up a thread with a high priority
    * Example: Disk controller has reported that the requested part of a file has been read into memory and the thread waiting for the file can continue execution
    * Example: System timer has told the kernel that your thread ran out of its time quantum
  * Voluntary switches occur when the thread explicitly requests rescheduling through a system call
    * Example: Thread explicitly requests to yield the CPU to some other thread
    * Example: Thread explicitly requests to be put to sleep or wait until a mutex is released
  * Semi-voluntary switches occur when the thread implicitly triggers rescheduling by performing some unrelated system call
    * Example: thread asks to read a file. 
      * Operating system requests the file from the disk controller
      * While the operating system is waiting to retrieve file contents from the disk controller, the operating system switches to another thread to avoid busy waiting
* There's some hardware protection level change to enter kernel state so that kernel logic/data can be accessed
* State for the interrupted thread needs to be saved
  * Might involve pushing all registers onto the thread stack and saving the stack pointer in the thread's Thread Control Block
* Many operating systems switch to an operating system-dedicated stack so that any operating-system internal stack requirements are not required by non-operating system stacks
* System call execution may change the set of ready threads by modifying the Thread Control Blocks from internal queues for different thread priorities
  * Example: Network card driver may have set an event or signaled a semaphore that another thread was waiting on, so now that waiting thread will be added to the ready set
  * Example: If a running thread calls sleep, and elected to remove itself from the ready set
* OS scheduler algorithm is executed to decide which thread to run next
  * Typically, the highest-priority ready thread that is at the front of the queue
  * If the scheduler decides to run a thread that belongs to a _different_ process from the one that owned the previously-running thread...
  * ...the memory-management hardware has to be laoded with the address space for the new process
    * This includes flushing the Translation Lookaside Buffer, which caches recent virtual-to-physical address translations
* The saved stack pointer from the Thread Control Block for the next thread is retrieved and loaded into the hardware stack pointer
* The core state for the selected thread is restored
* Return to user-level protection
* Interrupt-return is performed, transferring execution from the kernel to the selected thread
* In a multicore CPU, the scheduler may decide that a thread currently running on another core may need to be stopped and replaced by another thread that has become ready
  * There is an interprocessor driver to hardware-interrupt the core running the thread that has to be stopped
* There may also be extra page faults after a context switch from a thread in one process to a thread in another process
  * This is because some of the currently-running process memory may have been paged out in favor of pages belonging to the process that owned the previously-running thread
  
