# Special Case: Hyper Threads

* Individual therads cannot run concurrently
* All hyper-threads share almost all processing resources except for the register set
* Individual cores and CPUs still work in parallel
* So far, Intel's CPUs have at most two threads per core
* The CPU is responsible for time multiplexing the threads
* The real advantage is that the CPU can schedule another hyper-thread and take advantage of available resources such as arithmetic logic units when the currently running hyper-thread is delayed
  * This delay is usually caused by memory accesses
* If the single-threaded performance has a high cache hit rate, it is harder for hyper-threads to match that performance
  * The effective cache size for L1d or L2, etc available to each hyper-thread is cut in half since both hyper-thrads use the same cache to load their data
* If two hyper-threads execute completely different code the cache size will be cut in half, which means signficantly more cache misses
