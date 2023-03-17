# 9.8 Allocating Kernel Memory

* When a user process requests additional memory, pages are allocated from the free page frame list, maintained by the kernel
  * Even if a user process request only a single byte, internal fragmentation will occur, and the process will be granted an entire page frame
* Kernel memory is generally allocated from a free-memory pool that is different from the free page frame list that is available to user processes
* Certain hardware devices interact directly with physical memory (and do not interact with virtual memory) and may require memory that resides in physically contiguous pages
* The user process paging system may not be appropriate for kermel data structures which are often less than the size of the page (to avoid fragmentation)
