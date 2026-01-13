# [How DynamoDB global tables work](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/V2globaltables_HowItWorks.html)
* Replicates table data across AWS Regions
* A global table consists of two or more replica tables across different AWS Regions
* Each global table can only have one replica table per AWS region
* Thus, if a workload in a single AWS Region becomes impaired, the application traffic can shift to performing reads and writes to a different region
* There's multi-Region eventual consistency (default) and multi-Region strong consistency

## Multi-Region eventual consistency (MREC)
* MREC asynchronously replicates to all other replicas, typically within a second or less
* In the event that a replica in the MREC global table is isolated or impaired, any data will be replicated when the replica becomes healthy
* If the same item is modified in multiple Regions simultaneously, DynamoDB will resolve the conflict by using the modification with the latest internal timestamp on a per-item basis (last write wins)
  * An item will eventually converge in all replicas to the version created by the last write
* Strongly consistent read operations return the latest version of an item if the item was last updated in the Region where the read occurred
  * May return stale data if the item was last updated in a different Region

## Multi-Region strong consistency (MRSC)
* Items are synchronously replicated to at least one other Region before the write operation succeeds
* Strongly consistent read operation on any MRSC replica always return the latest version of an item
* Writes that attempt to modify an item already being modified in another Region fail with a conflict exception
  * These writes can be retried, and will succeed if the item is no longer being modified in another Region
* MRSC global tables are configured with three replicas or with two replicas + a witness
  * A witness contains data written to global table replicas but does not support reads or writes
  * A witness is located in a different Region than the two replicas
* MRSC global tables cannot span Region sets, like the US Region set and the EU Region set
* TTLs and local secondary indices are not supported for MRSC global tables

## Streams
* MREC global tables replicate changes by reading changes from a DynamoDB Stream on a replica table and then applying that change to all other replica tables
* Streams are enabled by default on all replicas in an MREC global table and cannot be disabled
* MREC replication may combine multiple changes in a short period of time into a single replicated write, resulting in each replica's Stream containing slightly different records
* MRSC global tables do not use DynamoDB Streams for replication, so Streams are not enabled by default
  * When Streams are enabled, records on MRSC replicas are identical for every replica, including Stream record ordering

## Transactions
* MREC global tables are only atomic within the Region where the operation was invoked
* Transactional writes are not replicated as a unit across Regions
  * This means that only some of the writes in a transaction may be returned by read operations in other replicas at a given point in time
* MRSC global tables do not support transaction operations and will return an error if those operations are invoked on an MRSC replica
