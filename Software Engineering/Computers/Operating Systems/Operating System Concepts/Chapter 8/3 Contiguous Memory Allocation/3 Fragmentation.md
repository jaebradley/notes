# 8.3.3 Fragmentation

* External fragmentation exists when there is enough total memory space to satisfy a request, but the available spaces are not contiguous
  * Memory is fragmented into a large number of small holes
* One solution to the external fragmentation problem is to let the logical address space of a process be noncontiguous
  * Allow a process to be allocated physical memory wherever such memory is available

## Internal Fragmentation

* Multiple partition scheme that has a hole of 18,464 bytes
* Next process requests 18,462 bytes
* Left with a hole of 2 bytes
* Overhead to keep track of this hole is larger than the hole itself
* To avoid this problem, break physical memory into fixed-sized blocks and allocate memory in units based on block size
* Memory allocated to a process may be slightly larger than the requested memory
  * In the case where a process requests block size + 1 bytes, then 2 blocks would be allocated to the process
* Difference between the memory allocated to the process and the memory requested by the process is internal fragmentation

## Compaction

* Shuffle all free memory together in one large block
* Not possible if address binding is done at assembly or load time
* Compaction is only possible if relocation is dynamic and is done at execution time
  * If dynamic, relocation moves the program and data to a different physical memory address, and then changing the base register to relect the new base address
* Simplest compaction algorithm is to move all processes toward one end of memory
  * All holes move in the other direction, producing one large hold of available memory
