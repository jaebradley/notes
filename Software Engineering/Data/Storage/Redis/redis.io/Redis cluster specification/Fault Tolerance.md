# [Fault Tolerance](https://redis.io/docs/latest/operate/oss_and_stack/reference/cluster-spec/#fault-tolerance)

## Heartbeat and gossip messages
* Redis Cluster nodes continuously exchange ping and pong packets (heartbeat packets)
* It is possible for nodes to send pong packets without triggering a reply
  * Used to broadcast a new configuration as soon as possible
* Node usually pings a few random nodes every second so that the total number of ping packets sent (and pong packets received) by each node is a constant amount, regardless of the number of nodes in the cluster
* Every node makes sure to ping every other node that hasn't sent a ping or received a pong for longer than `NODE_TIMEOUT` / 2
* Nodes also try to reconnect the TCP link with another node to make sure nodes are not considered unreachable due to a problem with the current TCP connection
* The number of globally exchanged messages can be large if `NODE_TIMEOUT` is small and the number of nodes is large since every node will try to ping every other node that they don't have fresh information for every `NODE_TIMEOUT` / 2

## Failure detection
* The two node flags used for failure detection are `PFAIL` and `FAIL`
* A node flags another node with the `PFAIL` flag when the node is not reachable for more than `NODE_TIMEOUT` 
  * Both primary and replica nodes can flag another node as `PFAIL`
  * Non-reachability means that there's an active ping pending for longer than `NODE_TIMEOUT`
    * An active ping is a ping that was sent that is still waiting for a reply
  * `NODE_TIMEOUT` must be large relative to the network round trip time
  * Nodes will try to reconnect with other nodes in the cluster as soon as half of the `NODE_TIMEOUT` has elapsed without a reply to a ping
  * `PFAIL` flag is information every node has about other nodes, and is not sufficient to trigger replica promotion
* A `PFAIL` condition is escalation to a `FAIL` condition when
  * Node `A` flags Node `B` as `PFAIL`
  * Node `A` collections, via gossip sections, information about the state of Node `B` from the POV of the majority of primaries in the cluster
* Node `B` will be marked as `FAIL` and a `FAIL` message will be sent to all reachable nodes
  * This message will force every receiving node to mark the node in a `FAIL` state regardless of it is has marked the node in a `PFAIL` state
* `FAIL` flags are cleared when
  * The node is reachable and is a replica as replicas are not failed over
  * The node is reachable and is a primary not serving any slots
    * Primaries without any slots don't really participate in the cluster and are waiting to be configured to eventually join the cluster
  * The node is reachable and a long enough time `(N x NODE_TIMEOUT)` has elapsed without any replica promotion
* Note that because the `PFAIL` -> `FAIL` transition is based on gossiping from the majority of primary nodes *over some period of time*
  * There are no guarantees that at any given point in time, a majority of primary nodes agree
* No way to ensure every node received the `FAIL` message
  * A node may detect a `FAIL` condition but due to a network partition not be able to reach any other node
