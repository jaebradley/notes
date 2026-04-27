# [A Practical Introduction to Kafka Storage Internals](https://medium.com/@durgaswaroop/a-practical-introduction-to-kafka-storage-internals-d5b544f6925f)

* Partitions are message storage units
* Topics are containers for partitions
* Creating a topic with three partitions means that three directories get created on file system
* Topic is just a grouping of partitions

## Partitions

* Immutable collection of messages - can only append messages to partition and cannot delete from them
* Messages sent to topic are distributed amongst partitions
  * First message might go to third partition
  * Second message goes to second partition
  * Kafka arbitrarily picks first partition and then distributes messages to partitions in round-robin fashion
  * By adding a key to the message, all messages with that key get stored in a single partition
* Messages added to a partition have their own auto-incrementing id or "offset"
* A partition has a one-to-one relationship with a broker (i.e. node)
  * If a partition exists on broker X it will not exist on any other brokers
  * A topic's partition can be spread amongst many brokers, but a single partition is only present on a single kafka broker

## Segments

* Collection of messages in a partition
  * Instead of storing all messages of a partition in a single file, Kafka splits them into chunks called segments
  * Makes it easier to purge data if data is past the retention policy
* There is an 'active' segment that Kafka writes to
  * Once segment size limit is reached, new segment file is created and it becomes the newly active segment
* Segment files are created with the offset of the first message
  * Segment 0 has messages from offset 0 to offset 5
  * Segment 5 has messages from offset 5 to offeset 10
* Index file keeps track of the position of a given offset
  * Use binary search to find offset value
  * Offset value gives position of message
  * Can go directly to position in log file to read

## Consumers and Partitions

* Only a single consumer per consumer group per partition
* This is to ensure that if a partition gets messages J, A, E (in that order) that the consumer will also read them in that order
* However, message order is not guaranteed at a topic level, with multiple partitions
* Increasing consumers doesn't increase parallelism
  * Need to scale partitions as well
  * If wanted to read data in parallel for two consumers, you'd need to create two partitions so each consumer can read from it's own partition

## Replication

* A replication factor of 2 means that Kafka will try to always ensure that each partition has a backup

```bash
Broker 0 | Broker 1

p1 (L)   |  p1 (R)
p2 (R)   |  p2 (L)
p3 (R)   |  p3 (L) 
```

* Partition 1 is on Broker 0 and is the leader
  * Partition 1 is also on Broker 1 and is the replica
* Kafka only reads and writes from leaders
* Replicas sync data from leader partition
* Replicas take over if leader goes down
* Leader and replica should never be on same broker

