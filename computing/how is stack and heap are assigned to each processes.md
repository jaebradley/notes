# [how is stack and heap are assigned to each processes?](https://softwareengineering.stackexchange.com/questions/325704/how-is-stack-and-heap-are-assigned-to-each-processes)

* There are two stacks, one for process 1 and another for process 2
* The processes operate completely independently and on a multi-cpu processor, can even operate concurrently
* A single processor can alternate between process 1 and process 2
  * When a processor stops running process 1 and starts running process 2, this is one form of context switching
  * The state of process 1 is saved and the state of process 2 is loaded
* A process usually reflects an address space
  * An address space is like memory as an array
  * Memory is shared between processes and the kernel, but conceptually, address space is a big byte of arrays starting at index 0
  * Each process is given its own address space, which allows each process to have its own stack and heap independent of the other processes without having to worry about conflicting indices / conflicting addresses
* A single process can run multiple threads
  * With multiple threads in a single process, the threads all share the same address space, though each thread still have their own stack
* When context switching between processes, the address space needs to change, but to switch between threads, only the stack space needs to switch
  * The threads within a process would share a common heap
