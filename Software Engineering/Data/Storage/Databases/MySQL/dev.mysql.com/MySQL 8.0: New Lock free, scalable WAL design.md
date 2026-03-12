# [MySQL 8.0: New Lock free, scalable WAL design](https://dev.mysql.com/blog-archive/mysql-8-0-new-lock-free-scalable-wal-design/)
* The WAL is called the "redo log" in InnoDB
* There's an in-memory buffer pool with modified pages that have not been flushed to disk yet
* InnoDB does not read from the WAL when the server is running - the WAL is only read during crash recovery
* Biggest technical challenge was preserving the total order of the dirty page list
  * There is a single dirty page list per in-memory buffer pool
  * Changes to pages are applied within "mini transactions"
  * These "mini transactions" allow atomically modifying multiple pages
  * When a mini transaction commits, the transaction writes its log records to the logger buffer
    * There is a global monotonically increasing Log Sequence Number (LSN)
  * In the old design, there were two mutexes to ensure the per-buffer pool dirty page lists were committed to the WAL in-order
    * One mutex was for the thread currently committing changes to the WAL
    * One mutex was for the thread currently waiting to commit changes to the WAL
  * With the removal of these mutexes, there was no similar ordering guarantees
* Another problem was "holes" that could occur when writing the full log buffer to disk since threads were not similarly blocked on waiting for the previous mini transaction to completely commit before attempting to commit their dirty page list like in the previous design
  * Example: Thread A commits Mini Transaction 1
    * Thread B starts committing Mini Transaction 2
    * Before Thread B is done fully committing Mini Transaction 2, Thread C could start (and finish) committing Mini Transaction 3
    * However, there could be gaps on disk between all the data committed as part of Mini Transaction 2 and the start of data committed as part of Mini Transaction 3
  * Solution was tracking which writes finished
    * Circular array data structure
    * Array slots are updated atomically and reused
    * A single thread is used to traverse and update array slots
    * This thread also keeps track of the maximum reachable LSN

### Recent Written
* Used for tracking committed writes to the WAL
* Provides the maximum LSN, such that all writes to the WAL with smaller LSN values, have been committed
* Potential crash recovery would end at the LSN tracked by the Recent Written data structure
* Array slots are traversed by the same thread that writes the log buffer to disk afterwards
  * This log writing thread updates the maximum LSN reachable in the log without holes

### Recent Closed
* Individual flush lists are protected by their internal mutexes
* No longer preserve the guarantee that dirty pages are added to flush lists in the order of increasing LSN values
* Checkpoint constraint: Do not write a fuzzy checkpoint at LSN = L2 if there is a dirty page for LSN = L1, where L1 < L2
  * This is because recovery starts at this checkpoint LSN value
* Flushing constraint: Flush list flushing should always be from the oldest page in the flush list
* The Recent Closed data structure tracks concurrent execution of adding dirty pages to the flush lists
  * Tracks the maximum LSM (M) such that all executions, for smaller LSN values, have completed
* Before a thread adds dirty pages to the flush lists, it waits until this M value is approaching
  * Then the thread adds the pages and then reports the operation to the Recent Closed data structure
* Example:
  * Mini transaction copied its log records to the log buffer for the LSN range between `start_lsn` and `end_lsn` 
  * The mini transactions must wait until it holds `start_lsn - M < L` where `L` is some configurable constant
  * After the condition holds, the mini transaction adds all the pages it made dirty to buffer pool-specific flush lists
  * If the `last_lsn` value for one of these flush lists is the LSN of the last page in the flush list (the earliest added page)
  * In the new design, it is guaranteed that all pages in the flush list have an oldest modification time that is greater than, or equal to, `last_lsn - L` 
  * When all pages are added to the flush lists, a finished operation between `start_lsn` and `end_lsn` is reported to the Recent Closed data structure
  * The log closer thread can traverse the finished appended pages, updating the maximum LSN, setting `M` to `end_lsn`

## Writing to disk
* Log writer thread writes the log buffer to the OS page cache
  * This is to write full blocks only to avoid the need to overwrite an incomplete block later
* Log flusher thread reads the log buffer and invokes `fsync`
  * Writing to operating system cache and writing to disk are driven by two different threads in parallel
  * Updates the `flushed_to_disk_lsn`

## When a transaction commits
* In the new design, the user thread executing the transaction waits until the `flushed_to_disk_lsn` is greater than or equal to the the LSN associated with the transaction
* When `flushed_to_disk_lsn` advances, the log flush notifier thread wakes up threads that are waiting on intermediate values of the LSN
  * Note that concurrently, the next `fsync` call could have started within the log flusher thread
* InnoDB relies on checkpoints where recovery would need to start
* Flushing dirty pages moves the checkpoint LSN forwards
* Allows free space in the redo log to be reclaimed
  * The blocks before the checkpoint LSN are considered free
* In the new design, there is a dedicated log checkpointer thread that monitors the oldest pages in the flush list and decides to write the next checkpoint
