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
* All of these sub-LRUs are governed by a single background thread called the LRU maintainer
* Each stored item has two bit flags - one for whether an items has ever been requested, and one for if an item has been accessed for a second time
  * This flag is unset when an item is unlink/re-linked or moved
* The `HOT` LRU acts as a probationary queue, since items are likely to exhibit strong temporal locality / very short TTLs
  * Items are never unlink/re-linked in the `HOT` LRU
  * Once an item reaches the tail of the queue, it will be moved to the `WARM` LRU if the item is active or the `COLD` LRU if it is inactive
* The `WARM` LRU acts as a buffer for scanning workloads, like web crawlers reading old posts
  * Only items which are requested twice could ever enter the `WARM` LRU
  * `WARM` items are given a greater chance of living out their TTLs
  * There is reduced lock contention as the only tail items are moved
    * Active tail items are unlink/re-linked back to the head of the `WARM` LRU while inactive items are moved to the `COLD` LRU
* The `COLD` LRU contains the least active items
  * Inactive items flow from the `HOT` and `WARM` LRUs to the `COLD` LRU
  * Items are evicted from the tail of the `COLD` LRU once memory is full
  * If an item becomes active, it will be queued up to asynchronously move to the `WARM` LRU
  * If there is a burst of item hits to the `COLD` LRU, this queue will overflow and items will remain inactive
* The `TEMP` LRU acts as a queue for new items and has a short TTL
  * Items in `TEMP` are never unlinked/re-linked, never flow to other LRUs
  * The `TEMP` LRU is disabled by default
  * Designed to save CPU and lock contention
* `HOT` and `WARM` LRUs are limited in size primarily by % of memory used
* `COLD` and `TEMP` LRUs are not similarly limited in size
* `HOT` and `WARM` LRUs also have a secondary tail age limit, relative to the age of the `tail` of the `COLD` LRU
  * Prevents very idle items from persisting in the active queues needlessly

### LRU Maintainer Thread
* Background thread that iterates over each sub-LRU and peeks at the tail item
* May move items / reclaim expired tail items
* Process unlink/re-link items from the `COLD` LRU

### Summary
* Items never unlink/re-link when directly fetched
  * Can fetch 500 keys at once and never have to wait for an LRU lock
* Items are unlink/re-linked asynchronously
  * Set/fetch traffic bursts can result in temporary imbalances in the `HOT` or `WARM` LRUs
* Mixing items with vastly different TTLs may waste memory
  * For example, mixing items with a 60 second TTL and a 1 day TTL will lead to the 60 second TTL items taking up space in the LRU until they are fetched or drop to the tail of the LRU

## LRU Crawler
* Single background thread that insets special crawler items into the tail of each sub-LRU in every slab class
* Crawer concurrently walks each crawler item backwards through the LRU from tail to head
* Crawler examines each item it passes to see if it is expired and reclaims the item if it is
* The Crawler builds a histogram of TTL remaining as it scans each sub LRU
  * The histogram informs the Crawler how aggressively to re-scan each LRU
  * Rescanning can take place every few seconds
* Since higher slab classes naturally have fewer items that take up more space, the Crawler can quickly scan and re-scan large items to keep a low ratio of dead memory
