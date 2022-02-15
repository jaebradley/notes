# [ZooKeeper: Wait-free coordination for Internet-scale systems](https://www.usenix.org/legacy/event/usenix10/tech/full_papers/Hunt.pdf)

## The ZooKeeper Service

### Service Overview

* ZooKeeper provides the abstraction of a set of znodes (data nodes) organized according to a hierarchical name space (hierarchical name spaces are commonly used in file systems)
* Znodes are data objects that clients manipulate through the ZooKeeper API
* All znodes store data and all znodes (except for ephemeral nodes) can have children
  * `/A/B/C` is the path to znode `C` where `C` has `B` as its parent and `B` has `A` as its parent
* ZooKeeper impelemnts watches to allow clients to receive timely notifications of changes without requiring polling
* When a client issues a read operation with a watch flag set, the operation completes as normal except that the server promises to notify the client when the information returned has changed
* Watches are one-time triggers associated with a session and are unregistered once triggered or the session closes
* Watches indicate that a change has happened, but do not provide the change

### Data Model

* Data model is essentially a file system or a key/value table with hierarchical keys
  * Hierarchical namespace is useful for allocating subtrees for the namespace of different applications and for setting access rights to those subtrees
* Unlike files in a file system, znodes are not designed for general data storage and instead store metadata used for coordination

### ZooKeeper guarantees

* Two ordering guarantees
  * Linearizable writes: all requests that update the state of ZooKeeper are serializable and respect precedence
  * FIFO client order: all requests from a given client are executed in the order that they were sent by the client
* A system comprising a number of processes elects a leader to command worker processes
  * When a new leader takes charge of the system, it must change a large number of configuration parameters and notify the other processes once its finished
  * As the new leader starts to make changes, we do not want other processes to start using the configuration that is being changed
  * If the new leader dies before the configuration has been fully updated, we do not want the processes to use this partial configuration
* In ZooKeeper, the new leader designates a path as the "ready" znode and other processes will only use the configuration when that "ready" znode exists
* The new leader makes the configuration change by deleteing the "ready" znode, updating configuration znodes, and creating the "ready znode
* If a process sees the "ready" znode, it must also see all the configuration changes made by the new leader due to ZooKeeper's ordering guarantees
* If the new leader dies before the "ready" znode is created, the other processes know that the configuration has not been finalized and do not use it
* Two clients A and B have a shared configuration in ZK and communicate through a shared communication channel
  * A changes the shared configuration in ZK and tells B of the change through the shared communication channel
  * B would expect to see the change when it re-reads the configuration
  * If B's ZK replica is slightly behind A's it may not see the new configuration
  * B can make sure it sees the most up-to-date information by issuing a write before re-reading the configuration
  * ZK provides the `sync` request, which causes the server to apply all pending write requests before processing the read without the overhead of a full write

## ZooKeeper Implementation

* At a high level, upon receiving a request, a server prepares it for execution
  * If such a request requires coordination among the servers (write requests) then they use an agreement protocol (atomic broadcast) and finally servers commit changes to the ZooKeeper database fully replicated across all servers of the ensemble
  * in the case of read requests, a server simply reads the state of the local database and generates a response to the request
* The replicated database is an in-memory database containing the entire data tree
* Each znode in the tree stores a maximum of 1MB of data by default
* Log updates to disk, force writes to beon disk media before they are applied to the in-memory database
* Keep a write-ahead log of committed operations and generate periodic snapshots of the in-memory database
* Every ZooKeeper server services clients
* Clients connect to exactly one server
* Read requests are serviced from the local replica of each server database
* Requests that change the state of the service are processed by an agreement protocol

### Atomic Broadcast

* All requests that update ZooKeeper state are forwarded to the leader
* Leader executes the request and broadcasts the change to the ZooKeeper state through Zab, an atomic broadcast protocol
* Zab uses simple majority quorums by default
* Use TCP for transport so message order is maintained by the network

### Replicated Database

* Each replica has a copy in memory of the ZooKeeper state
* When a server recovers from a crash it needs to recover internal state
* Replaying all delivered messages would take prohibitively long, so periodic snapshots + redelivery of messages since the start of the snapshot are used
* These snapshots are "fuzzy snapshots" since ZooKeeper state is not locked to take the snapshot
  * Instead, use a depth-first scan of the tree, atomically reading each znode's data and meta-data and write them to disk
  * Since the "fuzzy snapshot" may have applied some subset of the state changes delivered during the generation of the snapshot, the result may not correspond to the state of ZK at any point in time
  * Since state changes are idempotent, we can apply the state changes twice, as long as the state changes are applied in order
* Nodes `/foo` and `/goo` have values `f1` and `g1` and both are at version 1 when the fuzzy snapshot begins
* Stream of state changes
  * `SetDataTXN, /foo, f2, 2`
  * `SetDataTXN, /goo, g2, 2`
  * `SetDataTXN, /foo, f3, 3`
* After processing these changes, `/foo` and `/goo` have values `f3` and `g2`
* The fuzzy snapshot may have recorded that `/foo` and `/goo` have values `f3` and `g1` with versions `3` and `1`
  * This could happen if `/goo` was read from before right before the transaction was applied, and then `/foo` was read from right after the last `/foo` transaction was applied

### Client-Server Interfactions

* Servers process writes in order and do not process other writes or reads concurrently
* Servers handle notifications locally - only the servers that a client is connected to tracks and triggers notifications for that client
* Read requests are handled locally at each server
  * Each read request is tagged with a transaction id that corresponds to the last transaction seen by the server
* A read operation may return a stale value even though a more recent update to the same znode has been committed
* `sync` + read operation guarantees the latest updated value
* The follower server must be sure that the leader is still the leader
  * If there are pending transactions that commit, then the server does not suspect the leader
  * If the pending queue is empty, the leader needs to issue a null transaction to commit and orders the sync after that transaction
  * When the leader is under load, no extra broadcast traffic is generated
* If a client connects to a server and the client has a more recent transaction id than the server's transaction id, the server does not reestablish the session with the client until the server has caught up
* The client is guaranteed to be able to find another server that has a recent view of the system since the client only sees changes that have been replicated to a majority of the ZK servers
