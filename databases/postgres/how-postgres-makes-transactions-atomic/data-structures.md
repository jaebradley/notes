# [How `Postgres` makes transactions atomic](https://brandur.org/postgres-atomicity)

* `Postgres` uses multi-version concurrency control (`MVCC`)
  * Something naive like a single lock would not scale very well
  * Under `MVCC`, statements execute inside of a transaction
    * These statements, instead of overwriting data as they occur, create new versions of data
    * This new data is hidden until the transaction is committed
    * This means that other clients have access to the "original" data
* Transactions take a snapshot of data
  * This snapshot (as the name would imply) captures state of database at moment in time
  * Transactions are applied in serial order
  * A global lock ensures that only one transaction is being committed or aborted at a time
  * Database vacuums remove obsolete data (for example, rows that have been deleted)

## The `Transaction` data structure

* The `Transaction` data structure has two fields
  * `TransactionId` - this is the id for the top-level transaction
    * A `TransactionId` is only created for write transaction (i.e. read-only transactions never get a `TransactionId` assigned to them)
  * `MinimumTransactionId` - this is the minimum `TransactionId` for any active transactions when this transaction begins
  * The `MinimumTransactionId` is used by the vacuuming process to determine which transactions are safe to vacuum
    * The vacuuming process uses the minimum `MinimumTransactionId` (say that five times fast) of all active transactions as the minimum boundary of data to keep

## The `Tuple` / `Row` data structure

* `Postgres` rows are often referred to as `tuple`s
* A `Postgres` index doesn't store the `tuple`'s full set of data but rather the `tuple`'s `id`
  * This `id` is then used to retrieve `row` from physical storage (referred to as the `heap`)
* The `HeapTupleData` data structure has
  * A `DataLength` - which represents the size of the data
  * A `TableId` - which points to the table that the row lives in
  * A `Header` - which contains an array of fields
* The `HeapTupleHeaderData` structure contains a notable field, a `HeapTupleFields` structure
* The `HeapTupleFields` structure contains
  * A `MinimumTransactionId` field - which represents the `TransactionId` of the transaction where the row was created
  * A `MaximumTransactionId` field - which represents the `TransactionId` of the transaction where the row was deleted

## The `Snapshot` data structure

* `Postgres` uses `Snapshot`s to represent the state of the database at a given point in time
* A `Snapshot` contains
  * A `MinimumTransactionId` - which represents the minimum `TransactionId` for all in-progress transactions
    * The data before the minimum transaction should be visible to the current transaction
  * A `MaximumTransactionId` - is the last committed transaction id + 1 which represents the transactions that are invisible to the snapshot
  * `InProgressTransactionIds` - which represents an array of in-progress transactions
    * There could be some committed transactions that have an id greater than the `MinimumTransactionId`
    * This array is necessary because we want all committed transactions greater than the `MinimumTransactionId` to be visible, but any in-progress transactions to be invisible
