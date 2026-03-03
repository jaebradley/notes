# [Item Storage and Eviction](https://deepwiki.com/memcached/memcached/2.2-item-storage-and-eviction#item-storage-and-eviction)
* Items (key, value, metadata) are stored in memory, and accessed via a hash table for fast retrieval
* Items contain LRU and hash pointers
  * LRU pointers to next and previous items in LRU queue
  * Hash pointer to next item in hash bucket

## Item Retrieval
* Key is hashed to find appropriate hash bucket
* Hash chain for identified bucket is iterated over until matching key is found
* If the item's expiration time has not expired, item reference count is incremented and the item is returned
  * Reference counting is used to track when items can safely be freed
* Item's position in the LRU queue is updated

## Item Update and LRU Management
* New items are placed in the "hot" LRU
  * Short-lived items may be placed in the "temp" LRU
* Items in the "cold" LRU that are accessed are promoted to the "warm" LRU
* When memory pressur occurs, items are typically evicted from the "cold" LRU

## LRU-Based Eviction
* When allocating a new item, if there's no free memory from the slab allocator, the tail item of the "cold" LRU is selected
* This item is removed from the hash table
* This item is unlinked from the "cold" LRU
* Which should free memory for the new item
