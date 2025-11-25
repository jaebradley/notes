# [Overview of Redis Cluster main components](https://redis.io/docs/latest/operate/oss_and_stack/reference/cluster-spec/#overview-of-redis-cluster-main-components)
## Key Distribution Model
* Cluster's key space is split into 16384 slots
* Each primary node in a cluster handles a subset of the hash slots
* Hash Slot is calculated using `CRC16(key) mod 16384`

## Hash Tags
* Hash tags are a way to ensure that multiple keys are allocated in the same hash slot
  * `{user1000}.following` and `{user1000}.followers` will hash to the same hash slot

## Cluster Node Attributes
* Every node has a unique name in the cluster
* Node saves its ID and will use the same ID forever
  * It is possible for a given node to change its IP address without also changing its node ID
* Every node maintains the following information about the other nodes that it is aware of
  * Node ID, IP + port, the node's primary if it is a replica, last time the node was pinged, last time a pong was received, configuration epoch of the node, link state, and the set of hash slots

## Cluster Bus
* Every Redis Cluster node has an additional TCP port for receiving incoming connections from other Redis Cluster nodes

## Cluster Topology
* In a cluster of N nodes, every node has N-1 outgoing TCP connections and N-1 incoming connections
* TCP connections are kept alive all the time
* Nodes use a gossip protocol in order to avoid exchanging too many messages between nodes during normal conditions
