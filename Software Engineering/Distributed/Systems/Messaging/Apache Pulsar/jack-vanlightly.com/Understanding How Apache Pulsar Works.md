# [Understanding How Apache Pulsar Works](https://jack-vanlightly.com/blog/2018/10/2/understanding-how-apache-pulsar-works)

## Layer 1
* Messages stored in topics
* Topics are a log structure
* Each topic message is at an offset
  * Pulsar uses the term “cursor” to describe the tracking of offsets
* Producers send message to a given topic
* Pulsar guarantees that once the message has been acknowledged it won’t be lost
* Shared producers: multiple producers publish to the same topic
* Exclusive producers: only one producer can publish to a topic at a time. There is a “wait for” variant where the producer will wait until the topic becomes free.
* Consumers read messages from a topic via a Subscription
* A Subscription is a logical entity that keeps track of the cursor / consumer’s current offset
* Exclusive subscriptions only allow one consumer to read the topic at a time
* Shared subscriptions allow competing consumers to read the topic via the same subscription at the same time
* Key-shared subscriptions allow multiple consumers to read the same topic
  * The message key space is divided amongst the different consumers
  * Messages of any given key are always delivered to the same consumer
* Fail-over subscription allows active/passive consumers
  * If the active consumer dies, then the passive consumer takes over
  * There are never two active consumers at a time
* A topic can have multiple Subscriptions
* Cursors are stored on the Subscription
* Pulsar provides queueing semantics. Allows consumers to treat Pulsar topic like a queue that deletes messages after being ack-ed by a consumer.
* Pulsar provides log semantics where consumer can rewind their cursor if they want to.
* If no data retention policy is set on the topic, then messages are deleted once all cursors of attached subscriptions have passed its offset.
* Messages can be sent with an expiration
  * These messages will be deleted if they exceed the TTL even if they have not been acknowledged yet
  * In other words, they can be deleted before any consumer gets the chance to read them
* Consumers acknowledge their messages 1 by 1 or cumulatively.
* Cumulative acknowledgement is better for throughput but introduces duplicate message processing after consumer failures
* Cumulative acknowledgement is not available for shared subscriptions as acknolwledgement is based on the offset
* Partitions in Pulsar are also “just” topics
* Producer can send messages round-robin to the partitions, use a hashing algorithm, or choose a partition explicitly.

## Layer 2 - Logical Storage Model
* BookKeeper - general purpose log storage solution
  * stores data across a cluster of nodes (“bookies”)
* Zookeeper is used by Pulsar and BookKeeper for storing metadata and monitoring node health
* A “topic” is a stream of ledgers
* A ledger is a log
* The topic (i.e. the “parent” log) is composed from a sequence of ledgers (child logs
* Ledgers are appended to a topic
* Messages are appended to the ledger
* Once a ledger is “closed” it is immutable
* Ledgers are deleted as a unit - individual messages cannot be deleted
* Ledgers are broken into fragments
* Fragments are the smallest unit of distribution across a BookKeeper cluster
* Topics are a Pulsar concept
* Ledgers, fragments, entries are BookKeeper concepts
* Each ledger can be replicated across multiple bookies
* Each fragment is replicated across a different set of bookies if enough bookies exist
* A rack-aware policy means that BookKeeper will try to pick bookies from different “racks” (AZ in AWS, for example)
* Write Quorum is the numbef of actual bookies that Pulsar writes an entry to - must be <= the number of bookes available
* Write set = the set of bookes a given entry is written to
* When the write quorum is smaller than the number of bookes then there is “striping” - each bookie serves a subset of read/write requests
* Ack quorum is the number of bookies that must acknowledge the write
* Minimum guaranteed replication factor
* Ledger is create on new topic creation or when “roll-over” occurs
* Roll-over is when a ledger size or time limit is reached OR when ownership of the topic changes
* Fragment is created when a new ledger is created OR when a write to a bookie fails
* When a bookie cannot serve a write, the Pulsar broker creates a new fragment and ensures that the write gets acknowledged by Write quorum bookies.
* The data of a given topic is spread across multiple bookies
  * To grow cluster, add more bookies, and they will start being written to when new fragments are created
  * No rebalancing required
* Each Pulsar broker needs to keep track of the ledgers and fragments that each topic is comprised of (this information is stored in ZK).

## Layer 2 - Pulsar Brokers and Topic Ownership
* Pulsar brokers have no persistent state that cannot be lost
* A BookKeeper cluster does not perform replication
* Each bookie follows what is told by the Pulsar broker that owns the specific topic (each topic is owned by a single Pulsar broker)
* The Pulsar broker serves all reads and writes of that topic.
* When a Pulsar broker receives a write, it performs the write against the “ensemble” of the current fragment of that topic
* If no striping occurs, the ensmeble of each entry is the same as the fragment ensemble
* If striping occurs, then each entry has its onw ensemble, which is a subset of the fragment ensemble
* Once Ack Quorum brokers have acknowledged the write, the Pulsar broker will send an acknowledgement to the producer client. 
* If a Bookie responds with an error or does not respond, the broker will create a new fragment on a new ensemble of bookies that do not include the problem bookie
* Pulsar owner/broker serves reads
* It knows which offset has been persisted to BookKeeper
* Only needs to read from a single bookie
* When a broker fails / becomes unavailable, an ownership change occurs
* New broker becomes topic owner, all clients direct read/writes to this broker
* Fencing allows BookKeeper to guarantee that only one writer (Pulsar Broker) can be writing to a ledger

### Last Added Confirmed
* LAC is the commit index of a ledger
  * No read should pass this point
  * No consistency guarantees beyond the LAC
* Broker 2 updates the state of the current ledger of topic X to “In Recovery” from “Open”
* Broker 2 sends a fencing LAC read request to all bookies of the current fragment of the ledger
* Waits for write quorum - ack quorum + 1 responses - once these responses are received, the ledger is considered “fenced”
* Broker 2 takes the highest LAC response and then starts performing recovery reads from LAC + 1
* This ensures that all entries from that point onwards get replicated to write quorum bookies
* Once Broker 2 cannot read and replicate any more entries, the ledger is fully recovered
* Broker 2 changes the state of the ledger to “Closed”
* Broker 2 opens a new ledger and accepts writes to the topic

## Layer 2 - Cursor Tracking
Each subscription stores a cursor (current offset)
These cursors are stored in BookKeeper ledgers

## Layer 3 - Bookie Storage
* Write to bookie = message written to a journal file (WAL)
* Write is also made to a write cache
  * Accumulates writes (in-memory) and periodically sorts and fluxhes them to disk in entry log files
  * Sorted such that entries are grouped by their ledger
  * Entries written in strict temporal order would not be beneficial as sequential access *by ledger* is what matters most
* Write cache also writes to RocksDB, mapping the ledger id + entry id combination to the entry log id that the entry should exist in, and the offset within the entry log file
* Reads hit the write cache first (as it is the most up-to-date)
* On cache miss, reads hit the read cache
* On cache miss, requested entry is looked up in RocksDB
* Will update the read cache
* Write cache performs bulk writes to entry log files and RocksDB (async on background threads)
* Entry log files and RocksDB run on their own shared disk
* One disk for synchronous writes (to the journal file / WAL)
* Another disk for async optimized writes and all reads
* Writes will saturate ingress network bandwidth, reads will saturate egress network bandwidth, but they won’t impact each other’s network bandwidth

## Layer 3: Pulsar Broker Caching
* Topic has a single broker (“owner”)
* All reads and writes go through that broker
* Broker can cache the head of the topic in memory, so broker can serve up-to-date readers itself without making a network round-trip and possible disk read on a bookie
* Broker is aware of the Last Add Confirmed entry, and tracks which message is the last safely persisted message

