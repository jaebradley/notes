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