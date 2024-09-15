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