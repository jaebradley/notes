# [Write-Ahead Logging](https://www.sqlite.org/wal.html)
* All processes using the database must be on the same host computer
* WAL does not work over a network filesystem as the WAL requires all processes to share a small amount of memory

## How WAL Works
* Traditional rollback journal works by writing a copy of the original unchanged database content into a separate rollback journal file
  * Then the desired changes are written directly into the database file
  * In the event of a crash or rollback, the original content contains in the rollback journal is played back into the database file so that the database file is reverted into its original state
  * Commits occur when the rollback journal is deleted
* WAL approach inverts the traditional rollback journal approach
  * Changes are appended to a separate WAL file leaving the database file unmodified
  * Commits occur when a special record is appended to the WAL
    * Thus, commits can occur without ever writing to the original database
  * Multiple transactions can be appended to the end of a single WAL file

### Concurrency
* When a read operation begins, the operation remembers the location of the last valid commit record in the WAL (the "end mark")
* Many readers can have different "end marks" as the WAL is continually growing
* However, for a single reader, the reader's "end mark" value is unchanged for the duration of the transaction, ensuring that from the perspective of the transaction, it consistently views the database content as it existed at a single point in time
* Readers first check the WAL to see if the desired page exists in the WAL
  * If so, the reader reads the page that exists in the WAL directly prior to the reader's end mark
* If no page is found in the WAL, the page is read from the original database file
* Readers can exist in separate processes, so to avoid forcing every reader to scan the entire WAL to look for pages, a "wal-index" is maintained in shared memory to help readers locate WAL pages quickly
* Writers append new content to the end of the WAL file
  * Since there is only one WAL file, there can only be a single writer at a time
* By default, SQLite executes a checkpoint automatically when the WAL file reaches a size of 1000 pages
  * Checkpoints transfer content from the WAL file to the original database file
  * Checkpoint operations must stop when the checkpoint reaches a WAL page that is beyond the end mark of any current reader
  * This is because readers reference the original database file for relevant pages that might exist in the WAL, but are beyond the reader's end mark
  * So the checkpoint can't modify the original database file for these pages
  * The checkpoint remembers the last commit that it transferred so that it can resume transferring content from the WAL to the database when it is invoked again
  * This does imply that a long-running read transaction can prevent the checkpointer from making progress
* Whenever a write occurs, the writer checks how much progress the checkpointer has made
  * If the entire WAL has been transferred to the database and if no readers are making use of the WAL, then the writer will start writing new transactions at the beginning of the WAL
  * This prevents the WAL from growing too large
  * Overwriting is normally faster than appending

## Performance Considerations
* Write transactions are fast because the writes occur sequentially on disk
* Writers sync the WAL on every transaction commit if the `PRAGMA synchronous` setting is set to `FULL` but omit this sync if `PRAGMA synchronous` is set to `NORMAL` 
  * `NORMAL` mode means that the checkpoint operation is the only operation to issue a sync operation (\`fsync\` on unix)
  * This implies that another thread that is executing database queries will never block on a sync operation as that query's end mark will be after the commits that are being checkpointed
  * However, the downside with `NORMAL` mode is that transactions are not durable and there will be data loss following power failure
* Read performance deteriorates as the WAL file grows in size since each reader must check the WAL file for a potentially updated page
* Checkpointing requires syncing the WAL to persistent storage prior to moving content from the WAL into the database file
* The database file must be synced prior to resetting the WAL
* Checkpointer makes an effort to do as many sequential page writes to the database file as it can by transferring pages from the WAL to the database in ascending order
  * Even so, there are typically many disk seek operations interspersed among the page writes
* Default strategy is to allow transactions to grow the WAL until it is 1000 pages
  * For each subsequent commit, the checkpoint operation is executed until the WAL is reset to less than 1000 pages

## Implementation of Shared-Memory for the WAL-index
* Ordinary file that is mmapped
* Early pre-release implementations stored the wal-index in volatile shared-memory like files created in `/dev/shm` or `/tmp`
  * Process with different root directories would see different files
* Implementers could not find a method for creating nameless shared memory blocks on windows
* Only way to guarantee that all processes access the same database file using the same shared memory was to create an mmapped file in the same directory as the database itself
* Using a disk file means that there may be the opportunity to do unnecessary disk I/O by writing the shared memory to disk
* wal-index rarely exceeds 32 KiB and is not synced
* wal-index file is deleted when the last database connection disconnects 
