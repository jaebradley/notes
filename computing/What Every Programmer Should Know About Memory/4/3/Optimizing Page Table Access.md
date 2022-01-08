# Optimizing Page Table Access

* ALl the data structures for the page tables are kept in main memory
* Page tables are used to resolve every virtual address into a physical address using the page table walk
* At least one directory for each level is used in the process of resolving a virtual address - this requires up to 4 memory accesses, for a single access by the running process
  * These lookups cannot be parallelized since they depend on the previous lookup value
* The complete computation of the address of the physical page is cached
  * The non-page offset part of the virtual address is used as the tag for the cache
  * Many objects can share the same tag / physical address prefix (the lower bits of the virtual address are combined with the physical address prefix to get the complete physical address for an object)
* These physical address computations are stored in the Translation Look-Aside Buffer
  * Modern CPUs provide multi-level TLB caches
