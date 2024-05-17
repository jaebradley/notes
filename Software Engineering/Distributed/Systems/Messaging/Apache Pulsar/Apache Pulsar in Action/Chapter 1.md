# Chapter 1

* In an RPC architecture, an application invokes a procedure on a service that is running on a different host and must wait for that procedure call to return before it can continue processing

## Message Consumption Patterns

### Pub/Sub

* In Pub/Sub messaging, producers publish messages to named channels (topics)
* Consumers subscribe to these topics to receive the incoming message
* This topic receives and stores messages from multiple producers in the exact order in which they arrive
* Multiple consumers receive these messages via a subscription mechanism
* Pub/Sub is ideal for use cases that require multiple consumers to receive each message
* Also ideal for use cases where the order in which messages are received and processed are crucial (like a stock price service)

### Message queueing

* Messages are delivered in the order they are received
* Only one message consumer receives and processes an individual message vs. all consumers receiving a message
* Support high rates of consumption, scale up number of consumers in the event of a high number of backlogged messages
* To ensure that a message is processed exactly once, each message must be removed from the queue after it has been successfully processed and ack-ed by the consumer
* On consumer failure (i.e. message delivery was not ack-ed), the message is resent to another consumer
* Message will be processed out of order
* Well-suited for use cases where it is critical that each message is processed exactly once, but the order in which messages are processed is not important

## Distributed messaging systems

* The key architectural abstraction that makes this distribution of data possible is the write-ahead log
* WAL treats the contents of a message queue as a single append-only data structure that stores messages

## Partition-Centric Storage in Kafka

* Topic is divided into a fixed number of groups (partitions)
* Total storage capacity of the topic is now equal to partition count x size of each partition
* If there are 3 partitions, and these partitions live on nodes that have 4TB, 2TB, and 1TB of free disk space, then the last partition can only grow to 1TB in size, meaning that all partitions can only grow to 1TB in size
* Each partition is usually replicated across different nodes to ensure data redundancy
* So this means the minimum partition size is further reduced to the size of the smallest replica
* Rebalancing the entire topic (to increase capacity) implies copying the entire topic, from disk, transmitted over network, and then written to disk on the target brokers

## Segment-Centric Storage in Pulsar

* BookKeeper’s logical storage model is based on the concept of boundless stream entries stored as a sequential log
* Each BookKeeper log is broken down into smaller chunks (segments) of data
* These segments are comprised of multiple log entries
* These segments are written / duplicated across multiple node entries (“bookies”) for redundancy / scale
* These segments can be written anywhere that has sufficient disk space
* When there isn’t already sufficient storage capacity, new nodes can be added, and then used to store the segments
* True horizontal scalability as segments can be created infinitely and stored anywhere, unlike partition-based storage, which has some scaliing constraints placed on it based on the number of partitions

## Pulsar vs. Apache Kafka

* Pulsar decouples message storage and message serving
  * Kafka puts data processing and data storage on the same nodes
  * This was for simpler infrastructure and performance benefits (reducing network bandwidth, increasing speed)
* Pulsar serves data via stateless broker nodes and stores data via bookie nodes
  * Allows auto-scaling nodes up and down, which can impact cloud-based costs savings
  * Kafka limits the usefulness of autoscaling, because newly added nodes will not have any data to serve
    * Brokers can only serve requests for data that is stored on an attached disk
    * So these newly added nodes will not be able to read existing data from topics
    * These newly added nodes will only be able to handle write requests
* In Kafka, all consumers belong to a consumer group (a single logical subscriber for a topic)
* Each partition within a topic can only have one consumer at a time
* Partitions are distributed evenly across the consumers in the group
* If a consumer group has less members than partitions, then some consumers will be assigned to multipe partitions
* If there are more consumers than partitions, the excess consumers will remain idle, and only take over in the event of consumer failure
* The number of active consumers can never exceed the number of partitions in the topic
* So the only way to scale data consumption in Kafka is to more partitions in order to add more consumers to a consumer group (and increasing partitions is a costly operation)
* Kafka maintains message ordering within a specific partition, but not between different partitions in a topic
  * Thus message ordering is not maintained by a consumer group
  * When a new consumer is added to a group, it starts consuming messages from partitions previously consumed by another consumer
  * This shuffling (“rebalancing”) can have some undesirable consequences, like potentially data loss if consumer offsets are not saved before the rebalancing
* Common to have applications that have different consumption requirements
* Algorithmic stock trading application would need a high number of partitions in order to provide the necessary throughput to quickly process the topic data
* A data science team might want to the same stock data in the exact order they were received (i.e. a single partition topic to ensure global message ordering)
