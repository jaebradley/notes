# [Leaderless Replication](https://arpitbhayani.me/blogs/leaderless-replication)
* Strongly consistent by design, since it's based on majority node acknowledgement on write + majority node acknowledgement on read + nodes gossiping updates
* Designed to be fault tolerant as there is no single point of failure

## Writes
* Example of a 5 cluster database system, all nodes are primaries
* Client issues a write operation, and the operation is sent to all nodes
* Client waits for an acknowledgement from at least 3 nodes (majority) before marking the operation as a success, otherwise the client marks the operation as a failure
* Every record in the database has a monotonically increasing version number
* Each successful write increments this version number
* This version number makes it easy to identify the latest value for a given record
* If a write operation reached just 4 nodes because the 5th node was unavailable, when the 5th node recovers, it gossips with the other 4 nodes and identifies the writes it missed and eventually reaches consistency with the other 4 nodes
* Writes can still take time to propagate and sync across all nodes via gossiping

## Reads
* When the client needs to read data, it waits for a response from a majority of nodes
* Upon receiving responses from a majority of nodes, the client returns the value with the largest version number
* Given that a write is only considered a success when a majority of nodes return acknowledgement, if we also require a majority of nodes to respond to read requests, this guarantees that at least one node is sending the latest value of the record
