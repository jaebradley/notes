# [Replacing the cache replacement algorithm in memcached - Dormando (October 15, 2018)](https://memcached.org/blog/modern-lru/)
* When memcached was first deployed it was typically co-located on backend web servers
* It was important for memcached to not use too much CPU and not degrade related application performance
* The current application pattern is to have more CPU overhead and lower RAM overhead

## Original Implementation
* Standard doubly-linked list
* Items are inserted into the head of the list while evictions are popped off the tail
* If an item is accessed, it is unlinked from its position and then re-linked into the head
* No background threads to actively maintain data, so if an item expired, it would say in memory until it was accessed again
* A lookup for an expired item would result in a cache miss and freed memory
* Important optimization to only allow an item to be unlinked/re-linked to head of list every minute
  * Frequently accessed items avoid excessive mutex locks
* Fetching hundreds of items are likely to unlink/re-link at least a few of these items
  * Each unlink/re-link requires a mutex lock on the linked list, and there can be contention
* Scaling beyond 8 worker threads was difficult

## Segmented LRU
* Split into four sub-LRUs
* Each of these sub-LRUs have their own mutex lock
* All os these sub-LRUs are governed by a single background thread called the LRU maintainer
* Each stored item has two bit flags - one for whether an items has ever been requested, and one for if an item has been accessed for a second time
  * This flag is unset when an item is unlink/re-linked or moved
* The `HOT` LRU acts as a probationary queue, since items are likely to exhibit strong temporal locality / very short TTLs
  * Items are never unlink/re-linked in the `HOT` LRU
  * Once an item reaches the tail of the queue, it will be moved to the `WARM` LRU if the item is active or the `COLD` LUR if it is inactive
* The `WARM` LRU acts as a buffer for scanning workloads, like web crawlers reading old posts
  * Only items which are requested twice could enter the `WARM` LRU
  * `WARM` items are given a greater change of living out their TTLs
  * There is reduced lock contention as the only time an item is unlink/re-linked or moved is if for tail items
  * Active tail items are unlink/re-linked back to the head of the `WARM` LRU while inactive items are moved to the `COLD` LRU
