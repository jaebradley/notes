# [Kafka: All you need to know](https://medium.com/hacking-talent/kafka-all-you-need-to-know-8c7251b49ad0)

* Producers write records that can be read by one or more consumers
* These records are immutable and are stored in a distributed commit log
* Records are published to topics (which are like channels) and consumers subscribe to one or more topics to read records
* Consumers are grouped together in a consumer group so that one or more consumers can work together to consume records from a topic
  * New records are sent to one consumer inside a consumer group
* Brokers are nodes that store, receive, and send records
* Zookeeper is used to elect a new controller node
 * Zookeeper keeps track of all the brokers that are functioning and part of the cluster at any given time
 * Zookeeper keeps track of the existing topics, partitions within the topic, replicas, etc.
* Topics are split into partitions
  * Records are published to only a single partition in a topic
  * Partitions are owned by a single broker in the cluster
  * A partition may be replicated amongst multiple brokers so that in the case of a failure to the lead broker, another broker can take over leadership
  * Within a consumer group, each partition is assigned to only one consumer within a consumer group. This avoids two consumers reading the same record
* Consumers keep track of their offset (i.e. last record)
  * "Dumb broker, smart consumer"

