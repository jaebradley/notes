# [Pulsar Newbie Guide for Kafka Engineers (Part 5): Retention, TTL & Compaction](https://streamnative.io/blog/pulsar-newbie-guide-for-kafka-engineers-part-5-retention-ttl-compaction)
* Kafka does not consider whether a message is consumed - it will delete messages older than the retention period regardless of consumer status
  * Kafka brokers can delete old data even if some slow consumer hasn't processed it yet, thus meaning that the consumer would miss those messages
* Pulsar will keep all unacknowledged messages indefinitely
* Once a message is acknowledged by all subscriptions, Pulsar marks it for deletion
* Pulsar allows a retention period even after acknowledgement
  * So a policy like "retain messages for a day or when 1 GB in messages have been retained, whichever comes first, even after consumers ack them"
  * Allows attaching a new subscription within a day and reprocessing
* In Pulsar, compaction doesn't rewrite the existing data in one place
* Running compaction produces new compacted ledgers that contain the latest values per key
* Consumers choose to read from the compacted ledger if they want a compressed view of the topic
* Compaction can be triggered manually or be set to run periodically
* Broker will go through the topic's backlog and build a new ledger with only the latest message for each key
* After compaction, the topic has two sets of data: the full uncompacted backlog and a compacted snapshot
  * Pulsar keeps both because some consumers might want to read the full log and want to process every change while other consumers might just want the latest state
* Consumers choose to read from the compacted view by setting the `readCompacted` setting on the consumer to `true`
  * When `true`, the broker will serve from the compacted ledger for earlier data and then live data for new writes, providing an experience similar to Kafka's compacted topic
* Pulsar honors the concept of a null message as a deletion marker (tombstone)
  * If a message with key K and a null value is published, compaction will remove K from the compacted log
* Use case for compaction is a topic that holds the latest status of each user
  * Messages are produced where the user ID is the message key and the message value is the user's status
  * Compaction ensures that only the most recent status per user is kept in the compacted view
  * New consumers can read the compacted log from the start and get the latest state of all users without going through all historical changes
