# Multi-Level Page Tables

* 4KB pages are the norm on 32-bit machines and are often found on 64-bit machines
  * Offset is 12 bits, which leaves 20 bits as the selector of the page directory
* A table with 2 ** 20 entries is not ptractical
  * With each process having its own distinct page directory, much of the physical memory would be tied up in these page directories
* Use multiple levels of page tables, which form a large, sparse page directory
* The virtual address is split into 5 parts, 4 of which are indicies into directories
* The index corresponding to the highest level directory from the virtual address is used to get the index within that directory
* The value associated with this index is the address of the next directory
  * The index for the next directory is stored in the virtual address
* This process completes until the level 1 directory is reached, where the value associated with the level 1 directory entry is the high part of the physical address and the remaining bits of the physical address are completed by adding the page offset bits from the virtual address
* Each process running on the system might need its own page table tree
  * Ideally, used memory is close together in the virtual address space - the actual physical addresses used do not matter
  * A small program might get by with using just one directory at levels 2-4 and a few level 1 directories
    * 4kB pages and 512 entries per directory allow addressing 2MB with a total of 4 directories (1 at each level). 1GB of contiguous memory can be addressed with one directory for levels 2-4 and 512 directories for level 1
* The stack and the heap area of a process are allocated at opposite ends of the address space to allow either area to grow as much as possible
