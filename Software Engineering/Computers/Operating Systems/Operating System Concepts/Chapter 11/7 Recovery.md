# 11.7 Recovery

## 11.7.1 Consistency Checking

* Scan metadata on each file system
  * Occurs every time the system boots
  * Scan can take a long time
* Instead  of scanning, at the start of any metadata change, a status bit is set to indicate that the metadata is being updated
  * If all updates to the metadata complete successfully, the file system can clear that bit
  * If the status bit is set, a consistency checker needs to be executed
* `fsck` is Unix consistency checker
* Consistency checker compares data in the directory structure with data blocks on disk
* UNIX caches directory entries for reads
  * Any writes that result in space allocation or other metadata changes is done synchronously, before the corresponding data blocks are written
  * This is to avoid the loss of a directory entry in an indexed allocation system
  * Problems can still occur if the sycnhronous write is interrupted by a crash

## 11.7.2 Log-Structured File Systems

* All metadata changes are written sequentially to a log
* Each set of operations for performing a specific task is a transaction
* Once the set of operations are written to this log, they are considered committed
  * System call returns to the user process and continue execution
* The log entries are replayed across the actual filesystem structures
* Pointer keeps track of which actions have completed and which are still incomplete
* When an entire committed transaction is completed, it is removed from the log file
* Log file is a circular buffer
* Circular buffer writes to the end of its space, overwriting older values as writes continue
  * Cannot allow buffer to write over data it has not saved yet
* On system crash, any transactions have not been "replayed" on the file system, even if they have been "committed" by the operating system
* Transactions can be executed from pointer to keep file-system structures consistent

## 11.7.3 Other Solutions

* Transaction writes all data and metadata changes to new blocks
* When transaction is complete, the metadata structures that pointed to the old versions of those blocks are updated to point to the new blocks
* The file system can then remove the old pointers and the old blocks and make them available for reuse
* If the old pointers and blocks are kept, a snapshot is created
  * The snapshot is a view of the file system before the last update took place
* If the pointer updates from old to new blocks is done atomically, no consistency checking is necessary
* ZFS doesn't overwrite blocks
  * Check-sums of all metadata and data blocks
