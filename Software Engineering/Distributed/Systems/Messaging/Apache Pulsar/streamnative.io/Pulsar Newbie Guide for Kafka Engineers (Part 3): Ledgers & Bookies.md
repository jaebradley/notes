# [Pulsar Newbie Guide for Kafka Engineers (Part 3): Ledgers & Bookies](https://streamnative.io/blog/pulsar-newbie-guide-for-kafka-engineers-part-3-ledgers-bookies)
* Bookies are storage nodes and ledgers are append-only logs, spread across bookies to durably persist messages
* In Kafka, each broker stores data for the partitions it owns on its local disk
  * Replication is broker-to-broker
  * Partitions have leader and follower brokers
* Pulsar brokers do not do this and use BookKeeper as a separate storage layer
  * Pulsar brokers are stateless proxies/dispatchers
* Bookies are analogous to Kafka brokers, but they don't talk to clients
* Ledger = log
  * Replicated to some number of bookies
* Topics are composed of 1+ ledgers
  * As ledgers reach some size, the broker creates a new ledger for the topic
* Metadata around which ledger forms a topic is stored in ZooKeeper
* Need more storage or write throughput - add more brokers
  * In Kafka, adding brokers requires rebalancing data
* In Kafka, a single partition's writes are handled by one broker at a time (the leader)
  * In Pulsar, a topic's ledgers could be on many different bookies
* Only one writer appends to a ledger
  * Similar to how a Kafka partition is written by one leader
* Once a ledger is closed, it becomes read-only - similar to how Kafka log segments become immutable once closed
* Ledgers have unique IDs and the mapping of ledgers belonging to a specific topic is stored in ZooKeeper
* When a consumer asks to read data, the Pulsar broker handling that topic will read from the ledger 
* Cursor positions are stored in BookKeeper via special ledgers called cursor metadata
  * Acked message positions are durable
* BookKeeper uses multiple disks for WAL and storage without bottlenecks on a single disk
* Pulsar ensures ordering by having the broker orchestrate reads/writes of a topic's ledger in sequence
  * Broker knows the ledger sequence and the entry IDs within the ledger
* Since Pulsar Brokers do not hold persistent data (like Kafka Brokers) other brokers can pick up where the previous broker left off
  * In Kafka, broker failure can mean that partitions need a new leader and to wait for data to catch up
