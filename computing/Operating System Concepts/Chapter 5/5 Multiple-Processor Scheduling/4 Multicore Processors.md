# 5.5.4 Multicore Processors

* Symmetric Multiprocessing systems allow several threads to run concurrently by providing multiple physical processors
* Changes to hardware by placing multiple processor cores on the same physical chip (multicore processor)
* Each core has a register set to maintain its architectural state, and appears to the operating system as a separate physical processor
  * SMP systems with multicore processors are faster and consume less power than systems in which each processor has its own physical chip
* When a processor accesses memory, it spends a significant amount of time (up to 50% of its time) waiting for the data to become available
  * These memory stalls can occur due to a cache miss (accessing data not in the processor cache memory)
  * Multithreaded processor cores in which 2+ hardware threads are assigned to each core
  * If one thread is memory stalled, the core can switch to another thread
  * To the operating system, each hardware thread appears as a logical processor that is available to run a software thread
  * So a dual-threaded, dual-core system has four logical processors from the operating system's point of view
* Coarse-grained multithreading is when a thread executes on a processor until a long-latency event, like a memory stall, occurs
  * Processor switches to another thread (costly due to flushing the instruction pipeline before the other thread can begin execution)
* Two levels of scheduling for multithreaded, multicore processors
  * Operating system needs to decide which software thread to run on each hardware thread
  * Each core must decide which hardware thread to run
