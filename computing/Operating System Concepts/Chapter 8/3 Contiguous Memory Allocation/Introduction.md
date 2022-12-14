# 8.3 Contiguous Memory Allocation

* Memory is usually divided into two partitions - one for the operating system, and one for the user processes
* The interrupt vector is often at a low memory address, so the operating system is also usually at a low memory address
* Usually there are several user processes residing in memory at the same time
* One approach is to have each process contained in a single contiguous section of memory
