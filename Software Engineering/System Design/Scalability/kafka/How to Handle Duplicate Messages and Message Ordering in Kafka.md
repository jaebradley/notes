# [How to Handle Duplicate Messages and Message Ordering in Kafka](https://betterprogramming.pub/how-to-handle-duplicate-messages-and-message-ordering-in-kafka-82e2fef82025)

* A producer sends a message broker the following messages: `ORDER_CREATE`, `ORDER_MODIFY`, `ORDER_CANCEL` for a single topic that has a single partition - these messages would be received by the consumer in the order they were inserted
* Kafka sends all messages within a topic partition to a consumer in the same order which it gets from the producer
* Can split a single Kafka topic into many partitions
* Read / write operations for a topic partition happen independent of each other
* Having different partitions may lead to unexpected ordering if the `ORDER_CREATE` & `ORDER_MODIFY` messages are in a different partition (within the same topic) than the `ORDER_CANCEL` message
  * The consumers for both partitions may see the `ORDER_CANCEL` appear before the `ORDER_CREATE` or `ORDER_MODIFY` messages
* Kafka, by default, uses the modulo of the count of partitions as well as the key associated with the message to determine which partition a message ends up in
  * So if `ORDER_*` messages all have the same key, then they will all be sent to the same partition
* Kafka has a cursor / offset and any message that is "before" the offset is considered handled and not necessary to deliver any more to the consumer group
  * If the `enable.auto.commit` configuration parameter is set to `true` then Kafka will shift the offset as soon as it sends batched messages to consumers, thus guaranteeing messages are sent exactly once, regardless if the consumer was able to handle the message successfully
  * Setting the same parameter to `false` means that Kafka will only shift the offset after getting acknowledgement that all messages have been handled, leading to messages being sent at least once (and potentially more)
