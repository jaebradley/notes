# [What Happens When a Master or Replica Fails](https://arpitbhayani.me/blogs/handling-outages-master-replica)

## Replica outage
* A front-facing proxy can redirect read requests to another read-replica if the read is unable to be read by a read-replica
* When a replica crashes and recovers, it reconnects to the primary
  * Replica replicates from last known sequence number persisted to disk
  * Replica applies all changes that occurred on the primary, eventually ending up in a state of consistency

## Primary outage
* There is an orchestrator service that sends heartbeat pings / pongs to databases (including the primary database)
* Leader election amongst replicas to elect a new primary
* New primary information is conveyed to clients that attempt to connect to this new primary
* Primary passive standby that receives synchronous replication from the active primary
  * There is still asynchronous replication to the read replicas
  * Guarantees that the passive standby is always in sync with the active primary
