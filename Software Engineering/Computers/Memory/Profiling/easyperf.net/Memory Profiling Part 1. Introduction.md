# [Memory Profiling Part 1. Introduction](https://easyperf.net/blog/2024/02/12/Memory-Profiling-Part1)

## Memory Usage and Footprint

* Virtual memory size and resident memory size
* Virtual memory includes all memory that a process can access like the stack, heap, executable instructions, shared library instructions, memory that is swapped out to disk
* Resident memory size measures memory allocated to a process residing in RAM
  * Does _not_ include memory that is swapped out or has not been touched yet by the process
  * Does _not_ include memory from shared libraries that were not loaded into memory

### Example Point-In-Time Memory Usage for Program

* Process has allocated 200 KB to the stack and heap
  * Only 100 KB resides in main memory, the rest is swapped out or unused
* 500 KB binary, only 400 KB has been executed
* 2500 KB of shared libraries, only 1000 KB have been loaded into main memory
* Virtual Memory Size - 3200 KB
  * 200 KB (stack + heap)
  * 500 KB binary
  * 2500 KB shared libraries
* Resident Memory Size - 1500 KB
  * 100 KB (stack + heap)
  * 400 KB binary
  * 1000 KB of shared libraries

### Example Memory Usage + Memory Footprint Graph for Hypothetical Program

#### Memory Usage

* Resident memory size is always less than or equal to the virtual memory size
* Four example phases
* Phase 1 is the program start where it allocates memory
* Phase 2 is when the program starts using memory (memory usage stays constant)
* Phase 3 is when the program dallocates memory, and then ends up allocating even more memory
* Phase 4 has many objects that are allocated and then deallocated
* Virtual memory size may increase, but resident memory size may not
  * Memory reserved by an object, but that memory is never used

#### Memory Footprint

* Memory footprint is how much memory a process touches during a particular time period (like MB per second)
* Don't count how many times a certain memory location was accessed
  * If a certain memory location was loaded twice during a 100ms interval, count the "touched" memory only once
* Count number of bytes accessed by program that have never been touched before
* In the first second of the program's lifetime, most of the memory accesses are unique
* As the program starts using the memory allocation buffer, the number of unique memory accesses will decline
* In phase 4, the program is more memory intensive than before
  * The program accesses new ("unique") data in relatively large burts
  * These bursts are likely due to the relatively short-lived allocation, and then deallocation, of new memory regions
* If the memory footprint is small (e.g. ~1MB/s), and the resident memory fits into the L3 cache, the pressure on the memory subsystem should be low
