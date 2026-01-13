# [Amazon DynamoDB](https://en.wikipedia.org/wiki/Amazon_DynamoDB)
* Data is distributed into different partitions by hashing on the partition key
* Each partition can store up to 10 GB of data
* Each partition (by default) can handle 1k writes per second (1 KB item) and 3k strongly consistent reads or 6k eventually consistent reads per second (4KB item)
* Each partition features three nodes, which each contain a copy of the partition's data
* Each node contains two data structures: a B-tree to locate items, and a replication log that notes all changes made to the node
* Snapshots are taken periodically and are stored in S3 to support point-in-time restorations
* One of the three nodes in each partition is designated the leader
* All writes travel through the leader node, before propagating
* Leaders send heartbeats to other nodes every 1.5 seconds
  * Paxos algorithm leader election when nodes stop receiving heartbeats
* Indices result in substantial performance hits for writes and there can be five at most for any given table
