# [Best Practices for Postgres Database Replication](https://www.tigerdata.com/learn/best-practices-for-postgres-database-replication)
* Write-ahead logs maintain synchronicity between the primary and the replicas
* Replicas can be used for read traffic

## WAL streaming and replication
* Primary server continuously sends chunks of its WAL to one or more replica servers
* Replica servers then apply these logs to their own data files
* Transactions are written to the WAL before being committed
  * If server crashes the transaction can be recovered from the log
* WAL segments are streamed to replicas
  * Asynchronous by default but can be configured to have synchronous replication to ensure no data is lost during failure

## Asynchronous Replication
* Primary server does not wait for confirmation from the replica server before committing a transaction
* No guarantee that WAL records are received or have been applied at commit time
* There is risk of data loss where any transactions that were committed on the primary server but not yet replicated to the replica could be lost
* Use case: High performance over strict durability where some level of data loss is acceptable

## Synchronous Write Replication
* Primary waits until WAL records have been sent to and written to disk, but not necessarily applied by the replica server before confirming the transaction to the client
* Increased latency especially if network between primary and replicas have high latency / low bandwidth
* Replicas can still lag: the replica's WAL is updated but may not have been applied
  * Replica may not be immediately usable if the primary fails over until the WAL is replayed
* Use case: systems where zero data loss is critical but some delay in failover readiness is acceptable

## Synchronous Apply Replication
* Primary waits for WAL records to have been written and applied to the replica server before confirming transaction with the client
* Ensures that the replica is completely synchronized with the primary at commit time
* Even more latency added
* Guarantees zero data loss and that the replica is ready to take over immediately in the event of a failover
* Use case: data durability and immediate failover readiness is required where performance is a secondary concern

## Setting Replication Parameters
* `max_wal_size` determines the WAL file upper limit before WAL file recycling occurs
  * Impacts storage space and time needed for crash recovery
* `hot_standby` enables read-only queries on replica servers, offloading read workloads from the primary server
* `max_wal_senders` sets the maximum number of concurrent connection from replicas
* `wal_keep_size` specifies the minimum size of WAL files to retain for replicas
* `hot_standby_feedback` allows replicas to send feedback to the primary server about their current state, helping avoid query cancellation due to conflicts

## Excessive Locks
* When a query acquires an `ACCESS_EXCLUSIVE` lock on a table in the primary server, replicas must wait until the lock is released before they can replay changes
* Use `pg_locks` to monitor lock activity
