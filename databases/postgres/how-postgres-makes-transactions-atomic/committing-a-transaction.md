# [How `Postgres` makes transactions atomic](https://brandur.org/postgres-atomicity)

## Beginning a Transaction

* `Transaction`s aren't assigned an id until they start modifying data
* `Transaction`s don't get a snapshot until they run their first query
* There is a function `GetSnapshotData` that initializes a `Snapshot`'s data (`MinimumTransactionId`, `MaximumTransactionId`, `InProgressTransactionIds`)
* The `MaxTransactionId` is logged in shared memory
  * Whenever a `Transaction` commits, it notifies the `Postmaster` that it did and the `LatestCompletedTransactionId` updates (if the committed id is greater than the existing one)
* `MinimumTransactionId` and `InProgressTransactionIds` are calculated by iterating over all processes, and all active transactions in these processes

### `TransactionId` Wrapping

* `TransactionId`s are `32`-bit unsigned integers
* Once the maximum integer is acquired, the next id needs to "reset" to `3`
  * `TransactionId`s `0 - 2` are fixed
  * `TransactionId` `0` is for `InvalidTransactionId`s
  * `TransactionId` `1` is for `BootstrapTransactionId`s
  * `TransactionId` `2` is for `FrozenTransactionId`s

## Committing Transaction

* `CommitTransaction` function
  * Calls `RecordTransactionCommit` function
    * `RecordTransactionCommit` durably commits to `WAL` and updates `Transaction` ids in `pg_xact`
  * Then calls `ProcArrayEndTransaction` function
    * This will say that no transactions are in progress
    * Must be done **before** releasing locks but (obviously) **after** calling `RecordTransactionCommit`

### `RecordTransactionCommit`

* `Postgres` is durable against power-loss by using a write-ahead log (`WAL`)
  * Any database changes are written and flushed to disk (i.e the `WAL`)
  * If sudden termination occurs before these changes are committed to physical memory, `Postgres` can replay changes in the `WAL`
* Basically what `RecordTransactionCommit` does is
  * Checks to see if `Transaction` in question has an id
    * If it doesn't, there's no need to write a `COMMIT` record in the `WAL`
  * If a `Transaction` id *does* exist, write the `COMMIT` record
  * Flush the `WAL`
  * Update the `CLOG` or `Postgres`'s "commit log" (`pg_xact`) via the `TransactionIdCommitTree` function
    * `pg_xact` contains a summary of every transaction and if it's status (`committed` / `aborted`)
* Only committed `Transaction`s lead to an immediate flush to disk
  * In the case of an aborted `Transaction`, it's written to the `WAL` and commit log, but no need to flush because in event of a crash, there's no state loss (since they didn't actually update database)
  * `Postgres` notices these unflagged transactions during recovery and assumes they were aborted

#### `TransactionIdCommitTree`

* This function gets called in `RecordTransactionCommit` after a `Transaction` is written to the `WAL`
* This function is responsible for updating `pg_xact` / the commit log
* A "tree" is committed because a `Transaction` can have sub-`Transaction`s
* `TransactionIdCommitTree` cannot be guaranteed to be atomic
  * So each subcommit is recorded separately
  * Parent commit is recorded as the final step
  * `Postgres` won't consider subcommits actually "committed" until parent record is committed
    * This is because system could have recorded every subcommit but then crashed before it could write parent

### `ProcArrayEndTransaction`

* After `Transaction` has been recorded to commit log, the `ProcArrayEndTransaction` function updates `LatestCompletedTransactionId` in global memory
  * This process requires an exclusive lock on `LatestCompletedTransactionId`
* A "proc array" is the set of active `Postgres` processes (i.e. connections)
  * `Postgres` uses a process forking model to handle concurrency instead of threading
  * When it accepts a new connection, the `Postmaster` forks a new backend / process
* By incrementing the `LastCompletedTransactionId` value in global memory, any new `Snapshot` will use the updated value for it's `MaximumTransactionId`

## `HeapTuple` visibility

* Whether or not a `HeapTuple` is accessible by a `Snapshot` is determined by the `heapgetup` method
* It scans for qualifying `tuple`s
  * The first check for a qualifying `tuple` is one where the `MinimumTransactionId` for that `tuple` is before the `Snapshot`'s `MinimumTransactionId`
* Next, `Postgres` checks to see if the `MinimumTransactionId` associated with the `tuple` was committed
  * `Postgres` does this check by looking at the commit log - the commit log is considered the canonical source of commit status information
* If a `tuple` was committed, `Postgres` sets the "hint bits" for a `HeapTuple`
  * Checking the commit log for the status of a `tuple`'s commit status is expensive
  * `Postgres` sets commit status flags ("hint bits") for a scanned `HeapTuple` to avoid having to check the commit log every single time
