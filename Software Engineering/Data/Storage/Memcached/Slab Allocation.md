# [Slab Allocation](https://deepwiki.com/memcached/memcached/2.1-slab-allocation)
* Each slab class manages memory chunks of a specific size and maintains a free list of available chunks
* The memcached memory hierarchy
  * Total memory pool
  * Slab Pages: Fixed-size (typically 1 MB) pages assigned to slab classes
  * Chunks: Fixed-size memory blocks within a slab page
  * Items: Data stored in chunks with metadata headers
* The slab allocation initialization attempts to allocate total memory pool in one large chunk, if preallocation is enabled
  * Sets up slab class structures with calculated chunk sizes and number of chunks per slab
  * Preallocates a slab in each class to avoid "out of memory" errors
* Each slab class is configured with the size of each chunk and the number of chunks that fit on one slab page
* When items are no longer needed, their memory is returned to the appropriate slab class free list
* The slab allocator has the ability to reassign memory pages from one slab class to another
* When memory limits are reached
  * The global page pool is checked first for a free page
  * If there are no free pages, the LRU system is invoked to evict items from the relevant slab class
