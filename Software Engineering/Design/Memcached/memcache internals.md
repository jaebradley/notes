# [memcache internals](https://adayinthelifeof.nl/2011/02/06/memcache-internals/)

## LRU Algorithm

* Internally, all objects have a counter
* Counter holds a timestamp
* Every time a new object is created, the counter is set to the current time
* When a object gets fetched, it resets the counter to the current time

## Slab Allocation

* It partitions allocated memory into smaller parts (pages)
* Each page is 1MB large (the maximum size an object in memcache)
* Pages can be assigned a slab-class
  * Slab-class decides the size of the objects in the page called chunks
  * All chunks in a slab have the same size
  * Example: Slab class 1 (64B), slab class 2 (128B), etc
  * Multiple pages for each slab-class, but once a page is assigned a slab-class, it cannot be changed to another slab class
* Let's say there are slab classes of 100B and 128B
  * A data object of 105B would be stored in a chunk of the 128B slab class, since it is the first class where the 105B object would fit
  * Penalty of 23B that is unused
* If a complete page is full and another piece of data needs to be stored
  * Fetch a new free page, assign it to the specified slab-class
  * Partition the page into chunks, get first available chunk
  * In case where all pages are used, evict a chunk of the specified slab-class (even if there's a chunk from a different slab-class that is older)
  * Each slab-class has its own LRU

## Consistent Hashing

* Typical load balancing algorithm
    * Calculate a hash for each stored object's key
    * Hashes (should be) evenly distributed
    * Utilize modulo of the count of servers to calculate which server to store an object in
    * Once the number of servers changes, keys will change as well since the modulo calculation using the server count will produce a different result
* Consistent hashing (as dots on a clock aka a continuum)
  * If the continuum represents 16 bits then 0 and 65525 would be at 12, 32200 would be around 6, etc
  * Place servers on the continuum (well-distributed, hopefully)
  * Store a key that results in a 16-bit hash
  * Find the next server on the continuum that is "greater" than the calculated 16-bit hash (or wraps around to 0)
  * Benefit is that servers can be removed without impacting keys on other servers
    * If a key is associated with a different server, the calculation for that key will still point to that server (as that server is the next server on the continuum)
