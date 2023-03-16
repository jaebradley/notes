# [What is the difference between a process and a thread?](https://stackoverflow.com/questions/200469/what-is-the-difference-between-a-process-and-a-thread)

* One way of looking at a process is that it is a way to group related resources together
* A process has an address space containing program text and data
  * Other resources include open files, child processes, pending alarms, signal handlers
* A process also has a thread of execution ("the thread")
* The thread has a program counter that keeps track of which instruction to execute next
* The thread has registers, which hold its current working variables
* The thread has a stack, which contains the execution history, with one frame for each procedure called but not yet returned from
* Processes are used to group resources together while threads are entities scheduled for exection on the CPU
* Imagine a single thread of execution
  * There are times when the thread will be idle (like fetching data from main memory after a cache miss)
  * So now have two sets of thread state so that another thread can get work done while the other thread is waiting on main memory
* To have multiple threads, need a way to save CPU state (via registers) and maintain multiple stacks, which can be done in user space
  * Advantage of user space is that thread switching is fast since you don't have to trap into the kernel
* Microsoft Word and Chrome are two processes
  * Typing something in Microsoft Word and seeing it get saved automatically are examples of multiple threads (one for editing and one for saving) inside a process (Microsoft Word)
