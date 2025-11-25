# [Client and Server roles in the Redis cluster protocol](https://redis.io/docs/latest/operate/oss_and_stack/reference/cluster-spec/#client-and-server-roles-in-the-redis-cluster-protocol)
* Nodes are responsible for holding data, monitoring the state of the cluster, including mapping keys to the right nodes
* Nodes are also able to auto-discover other nodes, detect non-working nodes, and promote replicate nodes to primary nodes when needed to
* All cluster nodes are connected using a TCP bus and a binary protocol
* Every node is connected to every other node in the cluster using the cluster bus
* Nodes use a gossip protocol to propagate information about the cluster in order to discover new nodes, send ping packets to make sure all other nodes are working properly, send cluster messages to signify specific conditions
* Since nodes are not able to proxy requests, clients may be redirected to other nodes using redirection errors like `-MOVED` and `-ASK`
* Clients are not required to hold the state of the cluster, as the client should get redirected as needed

## Write Safety
* Asynchronous replication between nodes
* Last elected primary dataset eventually replaces all other replicas
* Two failure scenarios where acknowledged writes
  * Write reaches primary node
    * Primary node replies to the client
    * Write is not propagated to replicas via asynchronous replication between primary and replica nodes
    * If the primary dies without the write reaching the replicas, the write is lost forever if the primary is unreachable for a long enough period of time and one of the replicas is promoted
  * A primary node is unreachable due to a network partition
    * A replica takes its place as a primary node
    * After some time it is reachable again
    * Client with an outdated routing table writes to the old primary node before it was converted into a replica
* For a primary to be failed over, it must be unreachable by a majority of the primary nodes for at least `NODE_TIMEOUT` 
  * If a partition lasts more than `NODE_TIMEOUT` all the writes performed in the minority side may be lost
  * The minority side of a Redis Cluster will start refusing writes as soon as `NODE_TIMEOUT` time has elapsed without contact with the majority
