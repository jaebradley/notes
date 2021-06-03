# [Visualizing Kafka](https://timothystepro.medium.com/visualizing-kafka-20bc384803e7)

* Topics are addresses that producers can send messages to
* A service can listen and send messages to as many topics as it wants
* A consumer-group is when a group of services act as a single consumer
* For any message going to a consumer-group, Kafka routes that message to a single service (helps load balance messages)
* A topic acts as a queue for messages
  * A producer sends a message to a topic
  * The message is recorded and stored in the queue - the message is immutable
  * The message is then sent to any consumer of the topic
* A Kafka topic is really composed of many queues, called partitions
  * When a producer sends a message to a topic, the message gets routed to a single partition
  * Consumers still listen to all partitions and consume messages from all partitions
  * The topic determines which partition the message goes to
    * By default, a round-robin strategy is used - this can be configured to be based on user id, for example
    * Every message within a partition is guaranteed to be chronologically ordered
    * So if the partition was based on user id, the messages for a given user would be sent to any consumer in the order they were inserted into the partition
    * However, partitions do not guarantee order between themselves - in other words, partitions may send out their messages at any time
* Zookeeper maintains a set of Kafka cluster nodes where topics and partitions are stored
* Zookeeper duplicates messages to follower partitions after a message has been delivered to the leader partition
