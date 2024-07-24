# [Transaction Processing in PostgreSQL](https://www.postgresql.org/files/developer/transactions.pdf)

* Database files are accessed through shared buffer pool
  * Two different Postgres servers cannot see inconsistent views for a file
* Postgres does not support distributed transactions, so all commands of a transaction are executed by a single backend
* At time of writing, nested transactions are not supported
* Isolated characteristic of ACI(solated)D transactions means that transactions are not affected by the behavior of concurrently-running transactions
  * Serializable is the stronger variant of isolated and occurs when the final results of a set of concurrent transactions are the same as if we'd run the transactions serially in some order (though not necessarily any predetermined order)

## How changes are made "atomically"

* Tuple mutations are marked as `done` by the executing transaction as they are being made
* Concurrently running queries ignore the changes because they know the transaction has not committed yet
* When the transaction commits, all those changes become logically visible at once
* `pg_log` contains 2 status bits per transaction ID
  * These bits represent the states of `in progress`, `committed`, and `aborted`
* Normally, an aborted transaction will set its status bits to `aborted`
  * In the event of the transaction process crashing without having written the status bit, a Postgres server will check the state of the transaction
  * If the transaction is marked `in progress` but is not running on a server, the server will deduce that the transaction crashed and update the `pg_log` entry to `aborted`

## Is it really atomic and durable, even if the system crashes?

* Postgres transactions are only guaranteed to be atomic if a disk page write is an atomic action
  * True for most modern hard drives if a page is a physical sector
* `pg_log` is safe as these are bit flips that must be in the same physical sector
* When moving tuples around in a data page, there _is_ potential for data corruption if a power failure manages to abort a page write partway through

## Working through the Unix kernel costs us something too

* Critical that a transaction's data page changes are forced to disk before `pg_log` is written
  * Not doing so could lead to a power failure having a transaction marked as `committed` in `pg_log` but not having all data changes reflected on disk
* Unix kernels allow the ability to force the correct write order via `fsync` but the performance penalty of `fsync`ing many files is high

## Concurrent updates are tricky

* Example transaction: `UPDATE foo SET x = x + 1 WHERE rowid = 42`
* Transaction B comes along and wants to execute the same transaction on the same row (before the first transaction commits)
* B must wait to see if A commits
* If A aborts, then B can proceed using the existing value of `x`
* If A commits it's unclear what path B should take
  * Using the value at time of transaction start seems to yield an unacceptable result as `x` is updated by `1` and not `2`
  * However, if B is allowed to increment the new value of `x`, then B is reading data committed since it's execution start, violating the principle of transaction isolation

## Read committed vs. serializable transaction levels

* Read committed level means that transaction B is allowed to use the new tuple (i.e. A's transaction values)
  * Usually the default behavior
* Serializable level aborts transaction B with a not serializable error
  * Client application must redo transaction B, which will then mean that the new value of `x` will be visible under these strict serializable-behavior rules
  * Logically cleaner, but requires more application code
* A `SELECT` statement / transaction only sees data committed before it starts

## How it's implemented

* `tuple visiblity` - which versions of which table rows are seen by which transactions
* Ignoring tuples you're not supposed to see is the key to making transactions appear atomic
* `tuple` is a specific stored object in a table, representing one version of some logical table row
* A logical table row may have multiple versions simultaneuosly

## Non-overwriting storage management

* Store multiple versions of every row
* Tuple can be removed only after its been committed as a `deleted` row for long enough that no active transaction can see it anymore
* Removal of long-dead tuples is handled by a `VACUUM` maintenance command that is issued periodically

## Per-tuple status information

* Tuple headers include `xmin` (the transaction ID of the inserting transaction), `xmax` (transaction ID of replacing/deleting transaction - initially `NULL`), forward link to a newer version of the same logical row, if any
* Tuple is "visible" if `xmin` is valid and `xmax` is not
  * "Valid" means transaction was committed or is the current transaction

## "Snapshots" filter away active transactions

* Transaction A commits while Transaction B is running, don't want Transaction B to see Transaction A's updates partway through
* Make a list of currently executing transactions at the start of a transaction
  * Shared-memory table, where each Postgres server records the currently running transaction ID
* These transaction IDs are never considered valid by the current transaction, even if they are shown to be committed in `pg_log`
* No transaction with an ID greater than the current transaction's ever be considered valid

