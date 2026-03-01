# [LRU Crawler](https://deepwiki.com/memcached/memcached/2.3-lru-crawler#lru-crawler)
* Background thread system that scans item storage in LRU queues and hash table
  * Performs maintenance tasks like removing expired items, collecting metadata
* The main crawler thread executes on a sleep/wake cycle, waiting for a specific variable that is used to signal the start of a crawl
* Crawler modules all share an interface
  * There are methods to initialize a module before crawling starts, process each item encountered during a crawl, when a slab class is completed, and when a crawl is finished
* The three types of crawler modules are
  * An expired crawler that removes expired or flushed items
  * A metadump crawler that collects and outputs metadata about items
  * A Mgdump crawler that provides management information about items
* Crawler data structures are
  * `crawler` struct: holds state for each slab class being crawled
  * `crawler_module`: represents a crawler module instance
  * `crawler_client`: modules that need to communicate with clients
  * `crawlerstats`: to collect statistics about crawler operations
    * Tracks per-slab statistics
    * \# of items seen
    * \# of items reclaimed
    * TTL histogram
* Expired crawler module scans items and removes those that have expired or have been flushed
  * Keeps statistics of reclaimed items, items that have never been fetched, items with various TTL ranges
* Metadump crawler module generates a report of metadata for all cached items, including the item key, expiration time, last accessed time, check-and-set value, fetch status
  * Mostly used for diagnostics and analysis
* The LRU crawler iterates over enabled slab classes
  * For each class, extracts items from the LRU queue
  * Process each item using the active module
  * Updates any statistics
  * Moves to the next slab class after finishing with the current slab class
* Hash table crawler traverses the entire hash table, regardless of slab class
  * The crawler obtains a hash table iterator
  * Processes each iterated item using the active module
  * Useful for operations that need to see all items regardless of their LRU status
* Crawlers have control mechanisms like start, stop, pause, resume
* Crawlers sleep between batches of items
  * The length of sleep and the number of items to process between sleeps are both configurable
