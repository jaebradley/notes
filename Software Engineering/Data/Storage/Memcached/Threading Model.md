# [Threading Model](https://deepwiki.com/memcached/memcached/3.2-threading-model)
* Primary listener thread accepts new connections and dispatches them to worker threads
* Each worker thread independently handles assigned connections, processes request, and sends responses without coordination with other worker threads
* Each worker thread is represented by a structure that contains
  * A thread identifier
  * Event base for libevent
  * Communication pipes/eventfd for notifications
  * Connection queue
  * Thread-local statistics
  * Thread-specific caches
* Each worker thread runs its own event loop
  * After each event loop iteration, the worker thread submits IO queue operations
* Worker threads respond to two main types of events - connection events and I/O notifications
  * These use eventfd or a pipe to signal events
* Connection events include new connections, timeout notifications, thread pause/stop commands
* New connections are dispatched to worker threads using either round-robin or their network reception queue ID
* Connection queues are used to pass connection-related tasks between threads
* Each worker thread has its own connection queue where the main thread or other worker threads can place items
* Memcached supports asynchronous I/O operations through a queue-based system
  * I/O operations are queued in thread-specific I/O queues
  * Operations are submitted in batches
  * Completions are reported back to the original thread via the I/O notification system
  * External storage access operations can be performed without blocking the worker thread
* There is a `pause_threads` function that can pause all worker threads and all background threads
  * Used for operations that require thread coordination, like slab rebalancing
* The `stop_threads`  function gracefully shuts down worker threads and background threads
  * Stop the associated maintenance thread
  * Signal worker threads to stop
  * Stop background threads (LRU Crawler, LRU Maintainer, Slab Maintenance)
  * Close all connections
  * Join worker threads
* Each worker thread maintains thread-local caches
  * `rbuf_cache` for read buffers
  * `io_cache` for I/O objects
