# System Architecture

## System Interface

* `get(key)` operation locates object replicas associated with the key in the storage system and returns a single object or a list of objects with conflicting versions along with a context
* `put(key, context, object)` determines where the replicas of the `object` should be placed based on the assocaited `key` and writes the `replicas` to disk
  * `context` encodes system metadata about the object that is opaque to the caller (like version of the object)
  * Context information is stored alongside the object
* MD5 hashes the key to generate a 128-bit identifier that is used to determine the nodes responsible for serving the key

## Partitioning Algorithm

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

## Data Versioning

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

## Execution of get() and put() operations

* Any storage node is eligible to receive client get and put operations for any key
* Any node handling read / write is known as the coordinator
  * Typically, first among top N nodes in preference list
* Read / write operations involve first N healthy nodes in the preference list (skipping any that are down or inaccessible)
* Consistency protocol that depends on two values - the minimum number of nodes that must participate in a successful read operation and the minimum number of nodes that must participate in a successful write operation
  * Setting the Read number and the Write number such that Read + Write > N, yields a quorom-like system
  * Latency of get / put is disctated by slowest of R/W replicas
* When coordinator receives `put` request, it generates vector clock for new version and writes new version locally
  * Coordinator sends new version along with new vector clock to the N highest-ranked reachable nodes
  * If at least W-1 nodes respond then the write is considered successful
* For a get, the coordinator requests all existing versions of the data for that key from the N highest-ranked reachable nodes in the preference list
  * Waits for R responses before returning the result to the client
  * If coordinator gets multiple versions of the data it returns all versions that are causally unrelated
  * The divergent versions are reconciled and the reconciled version superseding the current versions is written back

## Handling Failures: Hinted Handoff

* Dynamo uses "sloppy quorum" where all read / write operations are performed on first N healthy nodes from preference list which will not always be first N nodes encountered when walking the consistent hashing ring
* Let's say a node A is down / unreachable during a write
  * Replica that lived on A will now be sent to node D
  * The replica sent to D will have a metadata hint suggesting which node was intended recipient of the replica (A)
  * Nodes that receive hinted replicas will keep them in a separate local database that is scanned periodically
  * Once A recovers, D will attempt to deliver the replica to A
  * Once the transfer succeeds, D may delete the object from its local store without decreasing the total number of replicas in the sytem
* Highly available storage systems must be capable of handling failures of entire data centers
* Each object is replicated across multiple data centers
  * The preference list for a given key is constructed such that storage nodes are spread across multiple data centers, connected via high speed network links

## Handling permanent failures: Replica synchronization

* Merkle trees are hash trees where the leaves are hashes of the values of individual keys
* Parent nodes are hashes of their respective children
* Each branch of the tree can be checked independently without requiring nodes to download the entire tree
* If the has values of the root of two trees are equal, then the values of the leaf nodes in the tree are equal and the nodes require no synchronization
* If node hashes are different, nodes can identify which hash values are different for children and continue this process until it reaches the leaves of the tree
  * Once at the leaves, can identify which leaf nodes are out of sync
* Merkle trees minimize the amount of data that needs to be transferred to synchronize
* Each node maintains a separate Merkle tree for each key range i.e. the set of keys covered by a virtual node
* Nodes can then compare whether keys within a key range are up-to-date
* Many key ranges change when a node joins / leaves system which means that these trees need to be recalculated

