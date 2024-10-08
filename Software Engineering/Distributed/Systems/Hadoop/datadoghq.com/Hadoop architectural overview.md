# [Hadoop architectural overview](https://www.datadoghq.com/blog/hadoop-architecture-overview/)

* Hadoop core components - Hadoop Distributed File System (HDFS), MapReduce, Yet Another Resource Negotiator (YARN), Zookeeper

## HDFS architecture

* HDFS provides scalable, fault-tolerant, rack-aware data storage and is the underlying file system of a Hadoop cluster
* Built for large datasets with a default block size of 128 MB
* Optimized for sequential operations
* Blocks are distributed throughout the Hadoop cluster
  * Each block is duplicated twice (total of 3 copies)
  * These blocks are duplicated in a different rack
* `NameNode` manages a cluster's file system namesapce and associated metadata (file-block mapping)
* `NameNode` acts as the leader and "broker" when accessing `DataNode`s
* `NameNode` operates in memory, persisting state to disk
  * Single point of failure for a Hadoop cluster
  * Typically state is persisted to two local disks (to combat single disk failure) and an NFS-mounted volume (to combat total machine failure)

## `fsimage` and the `edit` log

* `NameNode` stores file system metadata in two different files - the `fsimage` file and the `edit` log
* The `fsimage` file stores a complete snapshot of the file system's metadata at a specific moment in time
* Any incremental changes (renaming a file, appending a low number of bytes to a file) are stored in the `edit` log for durability instead of creating a new `fsimage` snapshot
* To restore state, a `NameNode` loads an `fsimage` file, performs transformations in the `edit` log, which restores the file system to the most recent state

## `NameNode` and `QJM`

* The Active and Standby `NameNode`s communicates with a group of `JournalNode`s
* When the Active `NameNode` makes changes, it logs a record of the change to a majority of the `JournalNode`s
* The Standby node watches the `JournalNode`s for changes to the edit log, and applies them to its own namespace
* `JournalNode` daemons have low overhead, so the daemons run on the same machines as the existing Hadoop nodes

## Untangling `YARN`

* `Yet Another Resource Manager` is the framework that assigns computational resources for application execution
* Consists of a `ResourceManager` (one per cluster), `ApplicationMaster` (one per application), `NodeManager` (one per node)
* An "application" is synonymous with a MapReduce "job"

### Typical Application Execution

* Client program submits MapReduce job to the `ResourceManager`
* `ResourceManager` negotiates a "container" (a collection of physical resources) for the `ApplicationMaster` and launches the `ApplicationMaster`
* `ApplicationMaster` boots, registers with the `ResourceManager`, and the original client program now interfaces directly with the `ApplicationMaster`
* `ApplicationMaster` negotiates resource bundles for the client program
* `ApplicationMaster` passes the "container" launch specification to the `NodeManager`, which launches a "continaer" for the actual job
* While the job / application executes, the client polls the `ApplicationMaster` for the job's status and progress
* When the job completes, the `ApplicationMaster` deregisters with the `ResourceManager`, returns containers to the resource pool

## Zookeeper

* Automatic `NameNode` failover requires a ZK quorum and a `ZKFailoverController` (`ZKFC`) process running on each `NameNode`
* The primary `NameNode` and the `Standby` NameNode maintain persistent sessions in ZooKeeper
* Primary `NameNode` holds a special, ephemeral, "lock" `znode` (file/directory in a regular file system)
* If the primary `NameNode` does not maintain contact with the ZooKeeper ensemble, the primary `NameNode`'s session is expired, triggering a failover
* The `ZKFC` periodically attempts to acquire the lock `znode` - if it can acquire a lock, the primary `NameNode` has failed
  * If a lock is acquired one of the standby `NameNode`s transitions to an active `NameNode`


