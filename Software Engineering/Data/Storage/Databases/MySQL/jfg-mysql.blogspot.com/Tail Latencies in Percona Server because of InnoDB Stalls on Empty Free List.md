# [Tail Latencies in Percona Server because of InnoDB Stalls on Empty Free List](https://jfg-mysql.blogspot.com/2022/11/tail-latencies-in-percona-server-because-innodb-stalls-on-empty-free-list.html)
* When using the InnoDB Empty Free List algorithm, the default in 5.6 and 5.7, and optional in 8.0, a query that needs a free page will wait until the LRU Manager Thread refills the free list
* The LRU Manager Thread is woken up at regular intervals, so the query is blocked until the LRU Manager Thread is done sleeping and completes its work

## InnoDB Free Pages and Free List
* All data operations (reads and write) happen in RAM, which really means in the InnoDB Buffer Pool
* Buffer pool is a memory buffer where InnoDB caches tables and index data as they are accessed
  * It has a finite size
* The buffer pool is organized into pages
* Pages are usually 16kb in size
* When some data that needs to be accessed is not in the buffer pool, InnoDB needs to fetch the relevant data before using it
* When the buffer pool is full and new data needs to be stored in RAM, some buffer pool pages need to be evicted to make room for new data
* A background InnoDB process keeps a buffer of free pages that can be quickly grabbed when fetching data - this is the free list

## Needing Free Pages With an Empty Free List
* InnoDB Buffer Pool Flushing is a background process that maintains the buffer pool
* When a buffer pool page is updated, the update does not immediately propagate to the corresponding file on disk
* Log entry for the change is written to the InnoDB Redo Log, and until the buffer pool page is written to disk, this buffer pool page is considered dirty
* Writing dirty pages is one of the responsibilities of the InnoDB Buffer Pool Flushing background process
* One type of InnoDB Flushing is LRU Flushing
  * The buffer pool is organized in an LRU data structure
  * LRU flushing keeps the LRU “tail” clean, by flushing dirty pages
  * The length of the tail elements that are kept clean is configurable
  * Free list is refilled by page cleaner threads that evict clean pages from the tail of the LRU and add them to the free list
  * Oracle MySQL implements multi-threaded LRU flushing where the page cleaners flush dirty pages, maintain the “clean” tail of the Buffer Pool, and refill the free list
    * When the free list is empty, a query thread that needs a free page will take on the responsibility of a page cleaner thread
    * It will attempt to evict a page from the tail of the LRU
    * When the tail of the LRU is clean, this is a cheap process
    * When the tail of the LRU is dirty, evicting a page means writing to disk
    * If there are many simultaneous queries that need free pages, there will be contention on the LRU mutex while single page flushes are executed
  * Percona Server uses LRU Manager Threads to maintain the “clean” tail of the Buffer Pool and refill the free list