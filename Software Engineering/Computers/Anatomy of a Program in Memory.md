# [Anatomy of a Program in Memory](https://manybutfinite.com/post/anatomy-of-a-program-in-memory/)

* Each process in a multi-tasking OS runs in its own memory sandbox
* This sandbox is the virtual address space, which in 32-bit mode is always a 4GB block of memory addresses
* These virtual addresses are mapped to physical memory by page tables
* Each process has its own set of page tables
* A portion of the virtual address space must be reserved to the kernel
  * Does not mean that the kernel uses that much physical memory, only that it has that portion of address space available to map whatever physical memory it wishes
  * Kernel space is flagged in the page tables as exclusive to privileged code, hence a page fault is triggered if user-mode porgrams try to touch it
* In a Linux process, the standard memory segments (bands in the address space) are the Kernel Space (1GB), the Stack (RLIMIT_STACK, usually 8MB), Memory Mapping Segment, Heap, BSS Segment (uninitialized static variables), Data Segment (Static variables initialized by the programmer)
* Linux randomizes the stack, heap, and memory mapping segment by adding offsets to their starting addresses to avoid security vulnerabilities
* Topmost segment in the process address space is the stack, which stores local variables and function parameters in most programming languages
  * Calling a method or funciton pushes a new stack frame onto the stack
  * The stack frame is destroyed when the function returns
  * The constant reuse of stack regions tends to keep active stack memory in CPU caches
  * Each thread in a process gets its own stack
  * It is possible to exhaust the area mapping the stack by pushing more data than it can fit
  * If maximum stack size has been reached, we have a stack overflow and the program receives a segmentation fault
  * The mapped stack area expands to meet demand, it does not shrink back when the stack gets smaller
* Heap provides runtime memory allocation, like the stack, meant for data that must outlive the function doing the allocation, unlike the stack
  * Heaps can become fragmented with used allocated memory blocks interspersed between free memory blocks
