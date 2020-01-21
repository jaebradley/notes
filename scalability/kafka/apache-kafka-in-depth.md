# [Apache Kafka In-Depth](https://medium.com/@sonusharma.mnnit/apache-kafka-in-depth-49aae1e844be)

* Messages are key/value pairs
  * Messages have an offset which serves as a sequential id for each message / record that is written
* Topics are just "feeds" which messages are published to and read from
* Kafka clusters consist of brokers (or nodes) in cluster that are maintained by Zookeeper
* Zookeeper is a distributed key/value store that is optimized for reads (writes are slower)
  * There are an odd number of znodes (known as an ensemble)
  * Since all reads and writes from a given partition for a particular topic occur through a replica leader, if leader goes down, new leader is elected by Zookeeper
* Kafka's message queue is maintained on disk instead of memory, so no loss of data in case of failover scenario
  * This also means that reading and writing to the queue is `O(1)`
  * Since the data is sequential, operations are generally pretty fast as modern operating systems allocate most of their free memory to disk-caching
* Once a producer gets metadata about a particular topic & partition, it writes message to leader broker and then followers replicate message
  * This write operation can be synchronous so that success is only reported when followers have copied message or asynchronous so that success is reported when leader has new message but followers have not confirmed write
* Consumers also look up topic & partition metadata and read from leader broker
* A consumer group is a set of consumers that share the same group id
* Each consumer in a consumer group connects to a particular partition where the partitions are split amongst the consumers equally
* A group coordinator is elected from one of the brokers in the cluster and is responsible for collecting heartbeats from consumers in the group
* Consumers in the group should be less than or equal to the number of partitions for a given topic or else there will be more consumers than partitions and one or more consumers will not process messages
* For a given partition in a given topic, Kafka creates segment files
  * These files have a naming pattern that includes the offset
  * So looking up a given message by offset is a matter of applying binary search to the different segment files, and then within the segment file itself
