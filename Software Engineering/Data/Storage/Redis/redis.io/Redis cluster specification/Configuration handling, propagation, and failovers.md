# [Configuration handling, propagation, and failovers](https://redis.io/docs/latest/operate/oss_and_stack/reference/cluster-spec/#configuration-handling-propagation-and-failovers)
* Epoch values are used to provide incremental versioning to events
* Every Redis Cluster node is created with a `currentEpoch` value of `0`
* Is a received' packet contains message headers indicating that the sender's epoch (sent via message headers) is greater than the receiving node's epoch, the receiving node's `currentEpoch` value is updated to reflect the sender's epoch
* Basically, the epoch serves as a logical clock for the cluster

## Configuration Epoch
* A new `configEpoch` is created during replica election
* For replicas, the `configEpoch` field represents the `configEpoch` of the replica's primary node when the replica and primary nodes last exchanged packets
* Primary nodes will not grant votes to replicas with an old configuration
* Every time the `configEpoch` changes it is permanently stored in the `nodes.conf` file
  * `fsync`-ed to disk when updated before a node continues operating
* `configEpoch` values generated during failover are guaranteed to be new, incremental, and unique

## Replica election and promotion
* Replica election occurs when a primary is in a `FAIL` state from the POV of at least one of the primary's replicas that fulfills the conditions to become a primary
* A replica needs to start an election and win it in order to promote itself to primary
  * Any replica can start an election but only one replica will win the election
* In order to be considered for an election, the replica's replication link must be disconnected from the primary for less than some configurable amount of time to ensure that the promoted replica's data is reasonably fresh
* In order to be elected, the replica must increment its `currentEpoch` counter and request votes from the primary instances
* The replica requests votes by broadcasting a `FAILOVER_AUTH_REQUEST` packet to every primary node in the cluster
  * Waits for at least 2 seconds, but at most `2 x NODE_TIMEOUT`
* Once a primary votes for a given replica with a positive `FAILOVER_AUTH_ACK`  it can no longer vote for another replica of the same primary node
* A replica discards an `AUTH_ACK` replies with an epoch that is less than the `currentEpoch` at the time the vote request was sent to ensure that votes are not counted for a previous election
* If replica does not receive ACKs from a majority of primaries within `2 x NODE_TIMEOUT` the election is aborted and a new one will be tried after `4 x NODE_TIMEOUT` 

## Replica Rank
* Replicas wait a short period of time before trying to get elected
  * This delay ensures that the primary's `FAIL` status propagates across the cluster
    * This prevents replicas from trying to get elected while the primary nodes are still unaware of the `FAIL` status of the failed-over primary node
  * This delay includes a random component which ensures that replicas do not start an election at the same time
* Replicas are ranked by the amount of replication data it has processed from the failed-over primary
  * The most up-to-date replicase try to get elected before others
* Once a replica wins election, it obtains a new unique and incremental `configEpoch` that is higher than any other existing primary's `configEpoch` value
  * This replica starts advertising itself as a new primary via ping and pong packets

## Primaries reply to replica vote request

* Primaries only vote a single time for a given epoch
* Primaries do *not* vote for an older epoch
  * Each primary has a `lastVoteEpoch` and refuse to vote when the `currentEpoch` in an election request packet is not greater than the `lastVoteEpoch`
* Primaries only vote for replicas where the replica's primary is flagged as `FAIL` 
* Primaries also ignore election requests where the `currentEpoch` is less than the primary's `currentEpoch` 
  * This guarantees that an old delayed reply from a previous primary can not be accepted for a new vote for a new election started by the same replica
  * Replica will increment its own `currentEpoch` when starting a new election

### **Practical example of configuration epoch usefulness during partitions**
* A primary is no longer reachable and has three replicas `A`, `B`, and `C` 
* Replica `A` wins election and is promoted to primary
* Network partition makes `A` unavailable for majority of cluster
* Replica `B` wins election and is promoted to primary
* Network partition makes `B` unavailable for majority of cluster
* `A`'s network partition is fixed and `A` is available again
* At this point, `B` is unavailable but `A` is available with the role of primary
* `C` is trying to get elected to fail over `B`
* `C` will successfully get elected since for the majority of primaries in the cluster `C`'s primary (\`B\`) is down
  * By winning election, `C` will obtain a new incremental `configEpoch`
* `A` will not be able to claim to be the primary for its hash slots because other nodes have the same hash slots associated with a higher configuration epoch compared to the one published by `A`
* All nodes will upgrade their tables to assign the hash slots to `C` and the cluster will continue operations

## Hash Slots Configuration Propagation
* Hash slots are propagated via heartbeat messages and update messages
* Ping and pong packets always add information about the set of hash slots that the primary is associated with
* If a receiver of a heartbeat packet finds the sender information is stale (due to the sender `configEpoch` and set of hash slots not being up-to-date) the receiver will send a packet with new information, forcing the stale node to update its info
* A new Redis Cluster node will have its local hash slot table initialized to `NULL` entries
* Rule 1: If a hash slot is unassigned and a known node claims it, modify the local hash slot table and associate the claiming node
  * So a heartbeat from node `A` claiming slots `1` and `2` with a configuration epoch value of `3`, an empty table would be updated to

```
0 -> NULL
1 -> A[3]
2 -> A[3]
....
16383 -> NULL
```

* Slot mappings can change when a replica replaces its primary during a failover and when a slot is resharded from one node to another
* Rule 2: If a hash slot is already assigned, and a known node is advertising the hash slot with a `configEpoch` that is greater than the `configEpoch` associated with the hash slot rebind the hash slot to the new node
  * Eventually all nodes in the cluster will agree that the owner of a slot is the one with the greatest `configEpoch` among the nodes advertising it
  * "Last failover wins"

## How nodes rejoin the cluster
* When node `A` is rejoining the cluster, it will be notified that hash slots 1 and 2 are now served by primary node `B`
* The count of hash slots served by `A` will drop to `0`
  * `A` will reconfigure to be a replica of the new primary (\`B\`)
* Primary and replicas change their configuration to replicate (i.e. be a replica of) the node that took its last hash slot
