# [Read-Your-Writes Consistency](https://arpitbhayani.me/blogs/read-your-write-consistency)
* Unavoidable replication lag when propagating writes from a primary database to a replication database
* Reads may go to a read replica before it has synced writes with a primary database
* Read-your-writes consistency is a system guarantee that once an item has been updated any attempt to read the record by the same client will return the updated value
  * No guarantees about other clients getting the updated value immediately after the write

## Synchronous Replication
* Every write operation on the primary database is not considered complete until it has finished replicating across all replication databases
* Thus, no matter which replication database serves a read request for the updated item in the future, it should always return up-to-date information
* Impacts write throughput and a faulty replication database will choke the entire system

## Pinning the Client to the Primary
* When a client performs a write operation, for a certain time window, all client requests are served by the primary database
  * This allows any async replication to fan out to read replicas
* After the time window, the client can read from any of the read replicas

## Fragmented Pinning
* Instead of having *all* client read requests served by the primary database, only read requests that are impacted by the write operation are served by the primary database
* All unrelated read operations are served by read replicas

## Primary Fallback
* This approach is for systems with lower replication lag
* Where anticipated access patterns for keys that exist
* All reads go to the read replica
* If a key doesn't exist in the read replica, for example, because it was recently created and hasn't been asynchronously replicated from the primary to the read replica, then the read request is then served by the primary database
* This doesn't work for mutable records
* Read replica effectively operates as a read-through cache of sorts
