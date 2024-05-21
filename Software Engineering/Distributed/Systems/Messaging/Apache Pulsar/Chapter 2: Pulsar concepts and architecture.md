# Chapter 2: Pulsar concepts and architecture

## Pulsar's physical architecture

* A Pulsar instance is one or more Pulsar clsuters that act together as a single unit
* Each instance has an instance-wide ZooKeeper cluster called the configuration store which retains information that pertains to multiple clusters (like geo-replication)

## Pulsar's layered architecture

* When a Pulsar client accesses a topic that has not been used yet
* Process is triggered to select the broker best-suited to acquire ownership of the topic
* Once a broker assumes ownership of a topic, it is responsible for handling all requests for that topic and any clients wishing to publish to or consume data from the topic need to interact with the corresponding broker that owns it
* Broker information is contained in ZooKeeper metadata and will change based on load rebalancing, etc.
* Cannot directly access brokers themselves, but instead communicate via the Pulsar proxy

## The Pulsar Proxy

* A "traditional" load balancer would route requests to a random broker
  * If a broker receives a request for a topic it isn't serving, it will automatically reroute the request to the appropriate broker
  * But this incurs a nontrivial penalty
* Proxy uses Pulsar's service discovery to determine which broker is hosting the topic you are trying to reach and route the client request to that broker
* Proxy will cache information in memory for future requests to streamline the lookup process
* Run more than one Pulsar proxy behind a traditional load balancer
  * Proxies can handle any (random) request so they can be "traditionally" load balanced without any issue

### Stateless serving layer

* Message data is stored separately from brokers
* Cluster can assign ownership of a topic to any broker in the cluster at any time
* Thus, brokers / serving layer are "stateless" since there is no information stored on the brokers themselves that is necessary to handle client requests

## Bundles

* All topics in a Pulsar cluster are assigned to a specific bundle
* Each bundle is assigned to a different broker
* Bundle assignment is determined by hashing the topic name
* This evenly distributes topics across all brokers

## Load Balancing

* When an existing bundle exceeds some preconfigured thresholds (due to changes in traffic patterns where a broker is serving several topics with heavy traffic) the buundle is split into two new bundles with one of them being offloaded to a new broker

## Load Shedding

* Broker offloads one or more bundles to a new broker
  * The selected bundles are left intact (i.e. no splitting occurs)
* Load balancing corrects the distribution of topics across bundles because one of the bundles receives much more traffic than others and we want to distribute the load evenly across all bundles
* Load shedding corrects the distribution of bundles across brokers based on the resources required to service them
  * Even though each broker can be assigned the same number of bundles, the message traffic handled by each broker could be dramatically different if the load is unbalanced across the bundles
* Scenario with 20 bundles handling 90% of the message traffic
  * If most of these bundles happen to be assigned to the same broker, the broker's resources (CPU, network, memory) could be exhausted
  * Offloading these bundles to another broker will help alleviate the problem
  * Splitting the bundle via load balancing would only shed ~50% of the message traffic

## Data Access Patterns

* Streaming system has three I/O patterns
  * writes - data is written to system
  * tailing reads - consumer is reading most recently published messages immediately after they have been published
  * catch-up reads - consumer reads large number of messages from the beginning of the topic (catch-up)
* Producer sends message to Pulsar
  * Immediately written to BookKeeper
* Once BookKeeper acks that the data was committed, broker sends a copy of the message to its local cache before acking the message publication to the producer
  * Broker can now serve tailing read consumers directly from memory and avoid disk access latency
* All non-acked messages are persisted until they can be delivered to and acked by consumers

## Logical Storage Architecture

* A Pulsar topic can be thought of as an infinite stream o messages, stored sequentially in the order they were received
* Incoming messages are appended to the end of the stream
* Consumers read messages based on whether they are tailing reads or catch-up reads
* Kafka separates streams into multiple replicas that are each stored entirely on a broker's local disk
  * Simple, fast since all writes are sequential, limits the amount the disk head has to move
  * Downside is that a single broker must have sufficient storage capacity to hold the partition data
* An Apache Pulsar topic is modeled as a series of segments (vs. a collection of partitions)
  * Once a segment is full, a new segment is created to hold new messages
  * Topic can be thought of as an unbounded list of segments, each containing a sugset of messages
* Managed ledgers in ZooKeeper retain the IDs of the data ledgers containing published messages
  * Data is published to topic A, and written to `ledger-20`
  * After the first 50k records, `ledger-20` is closed and another ledger (`ledger-245`) is created
  * Managed ledger retains the unique sequence of these data ledger IDs
  * Catch-up reads would start with `ledger-20` then `ledger-245` etc

## Bookkeeper Physical Architecture

* Each unit of a ledger is referred to as an entry
* Entries contain the actual raw bytes from messages as well as metadata to track and access the entries
  * Most critical metadata is the ID of the ledger to which the entry belongs
  * This ID is kept in the local ZooKeeper instance when a consumer attempts to read it in the future
* Ledgers are append-only
* Broker creates ledger, appends entries to the ledger and closes the ledger
* After a ledger has been closed, it can only be opened in read-only mode
* When entries in a ledger are no longer needed, the _whole_ ledger can be deleted from the system
* The BookKeeper servers responsible for the storage of ledgers are known as bookies
* When entries are written to a ledger, the entries are written across a subgroup of bookie nodes known as an ensemble
* Size of the ensemble is equal to the replication factor for the Pulsar topic, ensuring there are exactly R copies of the entry saved to disk to prevent data loss

