# [TigerBeetle Core System Architecture: Deconstructing Performance Engineering and the Power of Custom Interfaces](https://ixuvo.com/blog/tigerbeetle-core-system-architecture-performance-engineering)
* Database systems often have highly dynamic memory management
  * Database allocates memory for connection buffers, query plans, etc as queries arrive
* Modern memory allocators like `jemalloc` and `tcmalloc` are not immune to thread contention, memory fragmentation, latency spikes during peak load
* TigerBeetle eliminates dynamic memory allocation (`malloc`, `free`, etc) after its initialization phase
* When the TigerBeetle process starts, it calculates and allocates all the memory it will ever need for its lifetime
  * Includes memory for network buffers, storage cache, transaction logs, etc
  * After this initialization phase, the system runs entirely within pre-allocated static arrays and ring buffers
* Because memory is never freed and reallocated, heap fragmentation can never occur
  * System can never run out of memory mid-transaction due to fragmented free lists
* SInce there isn’t a memory manager searching for free blocks or running garbage collection, CPU cycles are dedicated to transaction processing
* Pre-allocated memory blocks can be aligned precisely to CPU cache lines (typically `64` bytes) and page boundaries (`4KB`) minimizing translation lookaside buffer misses
* Performance comes at a cost: the maximum number of concurrent connections, maximum batch size, maxmimum storage cache size must be defined at startup
* Workloads that exceed these pre-defined limits can lead to rejection of incoming requests
  * Trade-off is acceptable for financial systems where safety is more valuable than availability

## Custom Zero-Copy Interfaces and Kernel Bypass
* In a standard database, writing a transaction to disk involves copying data from user-space buffers to kernel-space page caches and then flushing those pages to physical storage
  * Usually involves multiple system calls, context switching, memory copies
* When TigerBeetle receives a batch of transactions over the network, the data is read directly into a pre-allocated static buffer
  * This buffer is registered directly with `io_uring`, Linux’s asynchronous I/O interface
* When these transactions need to be persisted to the WAL, TigerBeetle submits an I/O request to `io_uring` pointing to the memory address of the static buffer
* The kernel’s storage driver reads directly from the user-space memory block and writes the stored data to the NVMe controller via Direct Memory Access, bypassing the page cache
* TigerBeetle’s core data entities are fixed-size `128`-byte `struct`s
* `128` bytes is a multiple of standard CPU cache lines and disk sector sizes
* So these `struct`s can be perfectly packed into memory pages and disk sectors
* Eliminates the need for serialization / deserialization as the memory representation of `XYZ` `struct` in `Zig` is identical to its on-disk representation

## The Single-Threaded Execution Loop and VSR Consensus
* Many moden databases attempt to maximize throughput by parallelizing transaction execution across multiple CPU cores using locking mechanisms that introduce lock contention, thread synchronization overhead, and the risk of deadlocks
* TigerBeetle uses a single-threaded execution model where all transaction validation, balance checks, and ledger updates are executed sequentially on a single, dedicated CPU thread
* A single thread means that there is no overhead of thread context switching, mutex acquisition, page cache invalidation
* The execution thread can run at maximum CPU frequency, pulling batches of transactions from the lock-free ring buffer and processing them sequentially
* TigerBeetle groups transactions into large batches (up to 8,192 transactions per batch)
* There is a consensus layer that replicates these batches across the network to follower nodes
* Once a batch is committed by consensus network node quorum, the batch is processed by the single-threaded execution loop
* The batch is processed where the updated state is written to disk in a single write

## Memory Layout, Cache Locality, and Zig's Type System
* At the hardware level, speed of code is largely determined by how efficiently the CPU’s cache hierarchy is utilized
* Modern CPU can access registers in less than a nanosecond and L1 cache in  1 nanosecond
* Accessing main memory (RAM) takes 50-100 nanoseconds
* If a database engine is constantly chasing pointers across the heap, which occurs frequently in languages with many object references like Java, Go, Python, the CPU will spend most of its time stalled, waiting for data to arrive from RAM
* TigerBeetle maximizes cache localiity by keeping data contiguous in memory
* CPU’s hardware prefetcher can easily predict memory access patterns
* When processing a batch of transfers, the CPU pre-fetches transfers to the L1/2 cache can occur before the batch-processing execute thread even requests them
