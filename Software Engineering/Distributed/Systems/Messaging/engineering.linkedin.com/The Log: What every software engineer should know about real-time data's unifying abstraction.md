# [The Log: What every software engineer should know about real-time data's unifying abstraction](https://engineering.linkedin.com/distributed-systems/log-what-every-software-engineer-should-know-about-real-time-datas-unifying)
* Records are appended to the end of the log
* Reads proceed left-to-right
* Each entry is assigned a unique sequential log entry number
* Time is captured by the log entry number since entries to the left are older than the entries to the right
* Usage of logs in databases is to keep indices and various data structures in-sync in the presence of crashes
* To support atomicity and durability, databases utilize a log to write information about record modifications before applying the changes
* The log is the record of what happened and each table / index is the *projection* of this history into some useful data structure
* Since the log is immediately persisted, it is used as the authoritative source when restoring all other persistent structures in the event of a crash

## Logs in distributed systems
* Agreeing upon an ordering for updates in distributed data systems are part of the core design problems for distributed data systems
* State Machine Replication Principle: "If two identical, deterministic processes begin in the same state and get the same inputs in the same order, they will produce the same output and end in the same state"
* A program whose output is influenced by the particular execution order of threads or by a call to `gettimeofday` or some other non-repeatable logic is generally considered non-deterministic
* The purpose of the log is to squeeze all the non-determinism out of the input stream to ensure that each replica processing this input stays in sync
* The timestamps that index the log now act as the clock for the state of the replicas
  * Each replica can be described by a number - the timestamp for the maximum log entry the replica has processed
* Example of a replicated "arithmetic service" which maintains a single number value as its state
  * It "works" by applying addition and multiplication operations to this value
  * An active-active approach would log the transformations to apply, like "+ 1" or "x 2"
    * Each replica would apply these transformations and (eventually) end up transforming through the same set of values
  * An active-passive approach would have a single primary node execute the transformations and log the result, like `1`, `2`, `6`, etc. which the replicas would then store as their internal state

## Changelog 101: Tables and Events are Dual
* A log is similar to a list of all the credits and debits that a bank processes
* A table is similar to the set of current account balances
* A log of all historical credits/debits can be iterated over to create a table capturing the latest current state
* In this sense, the log is the more fundamental data structure
* The magic of the log, is that if it is a complete log of changes, it holds not only the contents of the final version of the table, but also allows recreating all other versions that might have existed
* Version control systems usually models a sequence of patches, which are in-effect, a log
  * Developers interact directly with a checked-out snapshot of the current code, which is analogous to the table
  * Replication also happens via a log: when a local checkout is updated, the patches since the last update are downloaded and applied to the current snapshot

## Log-structured data flow
* Each logical data source can be modeled as its own log
  * A data source could be an application that logs out events like clicks/page views or a database table that accepts modifications
* Each subscribing systems reads from this log, applying each new record to its own store
* Log acts as a buffer that makes data production asynchronous from data consumption
* When there are multiple subscribers that may consume from the same log at different rates, a subscriber can crash and catch up at a pace it controls
* Subscribers also do not need to concern themselves about the data source and whether the data originates from RDBMS, or some type of real-time query system

## Building a Scalable Log
* To allow horizontal scaling, Kafka "chops" up the log into partitions
* Each partition is a totally ordered log, but there is no global ordering between partitions
* Assignment of messages to a particular partition is controlled by the writer
* Most systems choose a partition by using some type of key, like a user ID
* Partitioning allows log appends to occur without co-ordination between shards
* Each partition is replicated across a configurable number of replicas, each replica has an identical copy of the partition's log
  * At any given time, a single partition will act as the leader
  * In the event of leader failure, one of the replicas will take over as the leader
* Kafka guarantees that appends to a particular partition from a single sender will be delivered in the order they are sent
* A log is easy to optimize for linear read and write patterns
  * Logs can group small reads and writes together into larger, high-throughput operations (batching)
  * Batching occurs from client to server when sending data, in writes to disk, in replication between servers, in acknowledging committed data
* Kafka uses a binary format that is maintained between in-memory log, on-disk log, and in-network data transfers
  * Optimizations like zero-copy data transfer

## Stateful Real-Time Processing
* Some sophisticated real-time processing might want to enrich an event stream, like a stream of clicks, with information about the user doing the click
  * Other systems might want to maintain a total click count, requiring keeping (and persisting) the current click count in state
  * Storing state on a remote storage system and joining over the network to that store requires a lot of network round-trips
* Stream processor keeps state in a local table / index on disk
  * Contents of this store are fed from the input streams
  * Journals out a local changelog to allow it to restore state in the event of a crash and restart
  * When the process fails, the state is restored from the changelog
  * The log is transformed into some local state that acts as an aggregated incremental record at some point in time
* The state of these stream processors is also maintained as a log
  * Other processors can subscribe to these journaled changelog entries

## The place of the log in system architecture
* System is divided into two logical pieces - the log and the serving layer
* Log captures state changes in sequential order
* Serving nodes store whatever index is required to serve queries
  * Key/value store might store something like a btree while a search system would store an inverted index
* Writes may go directly to the log, though they could also be proxied by the serving layer
* Writing to the log yields a logical timestamp (the index in the log)
* If the system is partitioned, then the log and serving nodes will have the same number of partitions
  * The log and serving nodes may have very different numbers of machines
* Serving nodes subscribe to the log and apply writes as quickly as possible to the serving node's local index
* Clients get read-your-write semantics from any node by providing the timestamp of a target write that the client would like to read
  * A serving node receiving this read request will compare the desired timestamp to its own log index point
  * The serving node will delay responding to the request until it has indexed up to at least that time to avoid serving stale data
