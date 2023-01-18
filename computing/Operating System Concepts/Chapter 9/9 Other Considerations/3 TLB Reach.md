# 9.9.3 TLB Reach

* Increasing the number of entries in the TLB can increase the hit rate, but expensive from both a cost and a power POV to add the associative memory used to construct the TLB
* TLB reach is number of TLB entries x the page size
* Ideally, the working set for a process is stored in the TLB
  * If not, then the process will spend time resolving memory references in the page table rather than the TLB
* Can increase the page size to increase TLB reach, but this will increase fragmentation for applications that don't need a large page size
* Operating system can provide multiple page sizes, but overhead of operating system for managing TLB with different page sizes
