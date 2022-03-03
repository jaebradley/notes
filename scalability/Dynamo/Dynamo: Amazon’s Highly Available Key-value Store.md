# [Dynamo: Amazon’s Highly Available Key-value Store](https://s3.amazonaws.com/systemsandpapers/papers/amazon-dynamo-sosp2007.pdf)

## Background

* Stateless services are services which aggregate responses from other services
* Stateful services are services that generate a response by executing business logic on its state stored in some type of persistent store
* Many services only store and retrieve data by primary key and do not require the complex querying and management functionality offered by an RDBMS

### System Assumptions and Requirements

* Simple read / write operations to a data item that is uniquelly identified by a key
* State stored as a binary object
* No operations span multiple data items - there is no need for relational schema
* Objects are < 1MB in size
* Data stores that provide ACID guarantees tend to have poor availability
* Dynamo targets applications that operate with weaker consistency but higher availability
* Dynamo does not provide any isolation guarantees

### SLAs

* At Amazon, we have found that metrics expressed in average, median, and expected variance are not good enough if the goal is to build a system where all customers have a good experience rather than just the majority
  * If extensive personalization techniques are used, then customers with longer histories require more processing which impacts performance at the high-end of the distributed
  * An SLA in terms of mean / median response times will not address the performance of this important customer segment

### Design Considerations

* Data replication algorithms traditionally perform synchronous replica coordination in order to provide a strongly consistent data access interface
* To achieve this level of consistency, these algorithms are forced to tradeoff the availability of the data under certain failure scenarios
* An example of this is that rather than dealing with the uncertainty of the correctness of an answer, the data is made unavailable until it is absolutely certain that it is correct
* Optimistic replication techniques allow changes to propagate to replicas in the background, and concurrent, disconnected work is tolerated
* Conflicting changes must be detected and resolved (so not only when to resolve these changes, but "who" resolves the changes)
* Dynamo is an eventually consistent data store meaning that all updates reach all replicas eventually
* When to perform conflict updates (for example, during reads or writes)
  * Many traditional data stores execute conflict resolution during writes and keep the read complexity simple
    * In such systems, writes may be rejected if the data store cannot reach all / majority of replicas
  * Dynamo has a goal of being an always writeable data store (i.e. highly available for writes)
    * For a number of Amazon services, rejecting customer updates could result in a poor customer experience
    * For example, the shopping cart service must allow customers to add and remove items from their shopping carts even amidst network and server failures
    * Thus, conflict resolution must be a responsibility of reads in order to ensure that writes are never rejected
* Who performs conflict resolution (for example, last write wins)
  * Either the data store or the application can do conflict resolution
  * Data store must have simple policies (again, "last write wins")
  * Applications are aware of the data schema and can decide on a conflict resolution method that is best suited for its client's experience
    * For instance, the shopping cart application can choose to merge conflicting versions and return a single unified shopping cart
    * Default policy can still be "last write wins"
* Dynamo favors decentralized peer-to-peer techniques over centralized control due to outages occurring in the past for centralized systems

## Related Work

### Discussion

* Applications that use Dynamo do not require support for hierarchical namespaces or complex relational schema
* Dynamo is built for latency sensitive applications that require 99.9%+ of read and write operations to occur within a few hundred milliseconds
  * Avoid routing requests through multiple nodes
  * Multi-hop routing increases variability in response times
  * Dynamo nodes maintain enough routing information locally to route a request to the appropriate node directly

## System Architecture

### System Interface

* `get(key)` operation locates object replicas associated with the key in the storage system and returns a single object or a list of objects with conflicting versions along with a context
* `put(key, context, object)` determines where the replicas of the `object` should be placed based on the assocaited `key` and writes the `replicas` to disk
  * `context` encodes system metadata about the object that is opaque to the caller (like version of the object)
  * Context information is stored alongside the object
* MD5 hashes the key to generate a 128-bit identifier that is used to determine the nodes responsible for serving the key

### Partitioning Algorithm

* Requires mechanism to dynamically partition data over a set of nodes (i.e. storage hosts)
* The output range for consistent hashing is treated as a fixed circular space / ring where the largest hash value wraps around to the smallest hash value
* Each node in the system is assigned a random value which represents its position
* Each data item's key value is assigned to a node by hashing the key, and then walking the ring clockwise to find the first node with a position larger than the item's position
* Each node becomes responsible for the region in the ring between it and its predecessor node in the ring
* Advantage of consistent hashing is that departure / arrival of a node only affects its immediate neighbors and other nodes remain unaffected
* The random position assignment of each node on the ring leads to non-uniform data and load distribution
* Dynamo doesn't map a node to a single point in the circle, but each node gets assigned to multiple points in the ring (a "virtual node")
    * A virtual node lookds like a single node in the system, but each node can be responsible for more than one virtual node
* When a new node is added to the system it is assigned multiple positions / tokens in the thing
* If a node becomes unavailable, load is redistributed evenly across remaining available nodes

## Replication

* Dynamo replicates data on multiple hosts
* Each key is assigned to a coordinator node
  * The coordinator is in charge of the replication of the data items that fall within its range
* Coordinator also replicates keys at N-1 clockwise successor nodes in the ring
* This results in a system where each node is responsible for the region of the ring between it and its Nth predecessor
* Note that with the use of virtual nodes, it's possible that the first N successor positions for a particular key may be owned by less than N distinct physical nodes (i.e. a node may hold more than one of the first N positions)
* Preference list for a key is constructed by skipping positions to ensure list contains distinct physical nodes
  * Preference list is the list of nodes that is responsible for storing a particular key

### Data Versioning

* A `put` call may return to its caller before the update has been applied at all replicas
  * A subsequent `get` operation may return an object that does not have the latest updates
* Add to cart / delete from cart are translated into put requests
  * If the latest version is not available, the add / delete is applied to the older version of the cart and the divergent versions are reconciled later
* Dynamo treats the result of each modification as a new and immutable version of the data and allows multiple versions of an object to be present in the system at the same time
  * When there is version branching, the client must perform reconciliation in order to collapse multiple branches of data evolution back into one
* Dynamo uses vector clocks to capture causality between different versions of the same object
* A vector clock is effectively a list of node and counter pairs
* One vector clock is associated with every version of every object
* If the counters on the first object's clock are <= all of the nodes in the second clock then the first is an ancestor of the second and can't be forgotten
  * Otherwise, the changes are considered in conflict and require resolution
* Vector Clock Example:
  * Client writes new object (`D1`) to node Sx (`[ [Sx, 1] ]`)
  * Client updates object (same node)
    * System now has object `D2` and the the vector clock is now `[ [Sx, 2] ]`
    * `D2` descends from `D1` and over-writes `D1`, however, there may be replicas of `D1` lingering at nodes that have not yet seen `D2`
  * Client updates the object again and a different server `Sy` handles the request
    * System now has data `D3` and its clock is `[ [Sx, 2], [Sy, 1] ]`
  * A different client reads `D2` and tries to update it and another node, `Sz` does the write
    * `D4` object, which descends from `D2` and a version clock of `[ [Sx, 2], [Sz, 1] ]`
  * A node that is aware of `D1` or `D2` could determine that upon receiving `D4` and it's clock that `D1` and `D2` are overwritten by the new data and can be garbage-collected
  * A node that is aware of `D3` and receives `D4` will find that there is no causal relation between them
    * There are changes in `D3` and `D4` that are not reflected in each other
    * Both versions of the data must be kept and presented to a client for semantic reconiciliation
  * Some client reads `D3` and `D4` where the context will reflect both values found by the read, and the context will also present a summary of the clocks
    * Client will perform reconciliation and node `Sx` will coorindate the write
    * `Sx` will update its sequence number in the clock and the new `D5` will have the clock `[ [Sx, 3], [Sy, 1], [Sz, 1] ]`
* Size of vector clocks may grow if many servers coordinate the writes to an object
  * Not likely since writes are usually handled by one of the top N nodes in the preference list, but can happen due to network partition and write requests are handled by nodes not in top N nodes in preference list
* Dynamo stores a timestamp indicating the last time the node updated the data item - this timestamp is stored along each entry in the vector clock
* When number of pairs in the vector clock reaches some threshold, the oldest pair is removed from the clock
* This truncation scheme can lead to reconciliation problems as the descendant relationships cannot be derived accurately, but has not surface in practice
