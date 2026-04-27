# [Memcached Architecture](https://medium.com/@hnasr/memcached-architecture-af3369845c09)

## Memory Management
* When allocating physical memory for arrays, strings, integers, etc, the actual physical memory is not guaranteed to be contiguous, but most likely will be scattered
  * Scattered physical memory leads to fragmentation - when there are gaps between allocated items
  * Fragmentation makes it difficult to find a contiguous block of memory that is large enough to hold new items
* Fragmentation is costly as there can be multiple I/O operations to retrieve many fragmented pieces of data that could have been stored in a single contiguous block of memory
* Memcached tries to avoid fragmentation by pre-allocating 1 MB (by default) memory pages
* As new memcached items are created, memcached will contiguously write items to the allocated pages
* This avoids fragmentation at the RAM level by moving memory management to memcached
* Pages are divided into equal size chunks and each chunk has a fixed size, determined by a slab class
  * Slab classes define the chunk size i.e. slab class 1 = chunk size of 72 bytes, etc
* Items will be stored in the most appropriate slab class, that is the slab class with a smallest chunk size that is still greater than the item's size
  * So if there are two slab classes, one with a chunk size of 72 bytes, and the other with a chunk size of 144 bytes and an item of 73 bytes needs to be stored, it will be stored in the slab class with a chunk size of 144 bytes, leaving 71 bytes unused
  * This is a form of fragmentation that mirrors physical memory fragmentation, but now occurs at the memcached page level

## Threading
* Memcached uses TCP as the transport layer
* The Memcached listener thread creates a TCP socket to listen to on port 11211
* Memcached allocates a thread pool for TCP connections
* When a new TCP connection is established, memcached allocates a thread from the thread pool to read data from the connection
  * This worker thread can handle multiple TCP connections

## LRU
* Every slab class has its own LRU
* The LRUs are implemented as a linked list
* If an item is accessed, it is moved from its current position to the head of the list
* Items that are not frequently used will eventually be moved to the tail of the list
* When memory is fully allocated, if a new item needs to be added, the tail item is deallocated and a new item is allocated in its place
* There are locks for the head of the LRU that are necessary to support concurrency
  * However, these locks also have a performance penalty and can slow down throughput

### LRU Locking
* Originally, memcached was designed with a single global lock
  * Thus, clients could not access two different items at the same time
  * This makes multi-threading less effective
* Memcached updated this locking model to support a global lock per slab class
  * Clients can access two different items from the same slab class
* Memcached further improved this LRU design by introducing sub-LRUs per slab class
  * These sub-LRUs (roughly) correspond to how recently an item has been used
  * There is still locking for items within specific sub-LRUs that require locking

## Read path
* To identify where an item lives in memory, memcached uses a hash table
* The item key is hashed and converted into an index within this hash table
* The value stored in the hash table maps to the location of the item on a specific page for the specific slab class associated with that item
* Memcached uses a chained hash table to manage collisions
  * The value of the hash table is actually a linked list of item location pointers
  * If an item is added, and that item's key hashes to the same hash table index, the linked list hash table value appends the added item's key + item location
  * When the item is looked up again, the linked list is traversed until the item's key is found within the linked list
  * Memcached monitors the length of these linked list and if they are too long, read performance will suffer
    * Memccached will do a hash table resize to flatten these chained values
* Update the slab class LRU to push the item to the head of the LRU, which requires obtaining a mutex lock on the LRU data structure
