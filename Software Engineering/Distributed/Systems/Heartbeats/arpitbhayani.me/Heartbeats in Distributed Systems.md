# [Heartbeats in Distributed Systems](https://arpitbhayani.me/blogs/heartbeats-in-distributed-systems/)
* In most implementations, the sender runs on a separate thread or as a background task to avoid interfering with the primary application logic
* The heartbeat receiver/monitor maintains state about all the nodes it is tracking
  * Typically storing the timestamp of the last received heartbeat for each node
* Most systems use heartbeat sending intervals in the 1-10 second range
* Typical rule of thumb is to set the timeout to 2-3x the heartbeat interval
* Short heartbeat intervals can consume network bandwidth, especially in clusters with hundreds or thousands of nodes
* Many systems follow the rule that the timeout should be at least 10x the round-trip time
* Many real-world systems use a hybrid approach where nodes send (push) heartbeats proactively  but the monitoring system also periodically polls critical nodes as a backup mechanism

## Phi Accrual Failure Detection
* Developed for the Cassandra database
* Instead of a binary output, the detector calculates a suspicion level
* Uses statistical analysis of historical heartbeat arrival times
* Maintains a sliding window of recent arrival times and uses this data to estimate the probability of when the next heartbeat should arrive
* If the heartbeat is late, the phi value increases gradually rather than jumping immediately to a failure state

## Gossip Protocols for Heartbeats
* Instead of reporting to a central authority, each node periodically exchanges heartbeat information with a randomly selected subset of peers
* Over time, information about the health of every node spreads throughout the entire cluster, much like gossip spreads in a social network
* Each node maintains a local membership list containing information about all known nodes in the cluster, including their heartbeat counters
* Periodically, the node selects one or more random peers and exchanges its entire membership list with them
* When receiving a membership list from a peer, the node merges it with its own list, keeping the most recent information for each node
* Eliminates single points of failure since every node participates in failure detection
* Scales because the number of messages that each node sends remains constant regardless of cluster size
* Nodes can fail and information will still spread as long as some nodes remain connected

## Implementation Considerations
* TCP provides reliable delivery and guarantees that messages arrive in order but is also introduces overhead and can be slower due to connection establishment and acknowledgement mechanism
* UDP is faster and more lightweight and packet loss is acceptable assuming the receiving can tolerate missing a few heartbeats without declaring a dead node
* Systems should account for network topology differences based on if nodes are located in the same datacenter vs. across the world
* Heartbeat processing should not have any blocking operations deferring expensive operations to worker threads
* Use event-driven architecture or thread pools to efficiently manage concurrent heartbeat processing

## Network Partitions and Split-brain
* Network partition occurs when network connectivity is disrupted, splitting a cluster into two or more isolated groups
* Nodes within each partition can communicate with each other, but cannot reach nodes in other partitions
* During a partition, nodes on each side will stop receiving heartbeats from nodes the other side
* This creates a scenario where both sides believe the other has failed
* Can lead to split-brain scenarios where both sides continue operating independently
* Systems often use quorum-based approaches
* During a partition, only the partition containing at least the quorum of nodes can continue operating normally
* The minority partition recognizes it has lost quorum and stops accepting writes
