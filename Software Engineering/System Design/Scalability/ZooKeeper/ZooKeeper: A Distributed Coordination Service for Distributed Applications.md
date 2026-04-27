# [ZooKeeper: A Distributed Coordination Service for Distributed Applications](https://zookeeper.apache.org/doc/r3.5.1-alpha/zookeeperOver.html)

## Design Goals

* Distributed processes coordinate with each other through a shared hierarchical namespace, organized similarly to a standard file system
* The namespace consists of data registers (znodes) similar to files and directories
* Unlike a typical file system, ZooKeeper data is kept in-memory, helps to achieve high throughput and low latency
* The servers that make up the ZooKeeper service must all know about each other
  * They maintain an in-memory image of state, along with a transaction logs and snapshots in a persistent store
  * As long as a majority of the servers are available, the ZooKeeper service will be available
* Clients connect to a single ZooKeeper service
  * Clients maintain a TCP connection which they send requests, get responses, get watch events, and send heart beats
  * If the TCP connection is broken, the client will connect to a different server
* ZooKeeper stamps each update with a number that reflects the order of all ZooKeeper transactions
* ZooKeeper performs best where reads are more common than writes (10:1 ratio)

## Data model and the hierarchical namespace

* A name is a sequence of path elements separated by a slash
* Every node in ZooKeeper's name space is identified by a path
* `/` is root path

## Nodes and ephemeral nodes

* Each node in a ZooKeeper namespace can have data associated with it as well as children
  * It is like having a file system that allows a file to also be a directory
  * ZooKeeper was designed to store coordination data (status information, configuration, location information, etc., so the data stored at each node is usually small)
* Znodes maintain a data structure that includes version numbers for data changes, ACL changes
  * Each time a znode's data changes, the version number increases
  * Whenever a client retrieves data it also receives the version of the data
* The data stored at each znode in a namespace is read and written atomically
  * Reads get all the data bytes associated with a znode and a write replaces all the data
  * Each node has an Access Control List that restricts who can do what

## Implementation

* The replicated database is an in-memory database containing the entire data tree
* Updates are logged to disk for recoverability
* Writes are serialized to disk before they are applied to the in-memory database
* Read requests are serviced from the local replica of each server database
* As part of the agreement protocol, all write requests from clients are forwarded to a single server called the leader
* The rest of the ZooKeeper servers are the followers, and they receive message proposals from the leader and agree upon message delivery
  * The messaging layer takes care of replacing leaders on failures and syncing followers with leaders
* ZooKeeper uses a custom atomic messaging protocol
  * The messaging layer is atomic so ZooKeeper can guarantee that the local replicas never diverge
* When the leader receives a write request, it calculates what the state of the system is when the write is to be applied and transforms this into a transaction that captures this new state
