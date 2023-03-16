# [Why software developers should care about CPU caches](https://medium.com/software-design/why-software-developers-should-care-about-cpu-caches-8da04355bb8a)

* A typical processor has four CPU cores
  * Each core has an L1 cache (max ~64kb) and an L2 cache (~256KB)
  * All cores share an L3 cache (~ 8MB)
* Rough access latency comparison
  * L1 ~4 CPU cycles
  * L2 ~11 CPU cycles
  * L3 ~39 CPU cycles
  * Main Memory ~107 cycles
* A cache line is the unit of data transfer between the cache and main memory
  * Typically, 64 bytes
  * The processor will read or write an entire cache line when any location in the 64 byte region is read or written
  * Processors attempt to prefect cache lines by analyzing the memory access pattern of a thread
* Imagine a vector of object pointers
  * The objects are allocated on the heap, and it is quite likely that all the objects are not adjacent in terms of their memory addresses
  * This might mean that accessing each object results in loading a complete cache line
* Consider instead a vector of objects
  * These objects would be organized in contiguous memory and thus would minimize the number of cache lines to load
