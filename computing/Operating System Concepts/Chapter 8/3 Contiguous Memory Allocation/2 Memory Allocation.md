# 8.3.2 Memory Allocation

## Multiple-Partition Method

* Divide memory into several fixed-sized partitions
* Each partition contains exactly one process
* When a partition is free, a process is selected from the input queue and is loaded into the free partition
* When a process terminates, the process becomes available for another process
* No longer in use

## Variable-Partition Scheme

* Operating system keeps a table with the available blocks of memory
* When a process is loaded into memory, it can then compete for CPU time
* When a process terminates, it releases memory, which the operating system can fill with another process from the input queue
* Memory is allocated to processes until the memory requirements of the next process cannot be satisfied (no available block of memory can satisfy the process's memory)
  * Operating system can wait until a large enough block of memory is available or iterate over the input queue to see if a process can fit the available memory
* Scattered memory blocks of various sizes (holes)
* Operating system searches set set of available memory blocks for a hole that is large enough for this process
  * A hole that is too large is split, with the remaining memory returned to the set of memory blocks available
  * When a process terminates and a block of memory is released, if it is next to another free block of memory, the two blocks are merged
* First fit strategy is allocating the first hole that is big enough
  * Can stop searching the list of free holes once a match is found
* Best fit strategy is allocating the smallest hole that is big enough
  * Have to search all free holes, unless the set of free holes are ordered by size
  * Produces smallest leftover hole
