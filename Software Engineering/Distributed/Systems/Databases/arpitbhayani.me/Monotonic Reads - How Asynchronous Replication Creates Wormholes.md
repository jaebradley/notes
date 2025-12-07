# [Monotonic Reads - How Asynchronous Replication Creates Wormholes](https://arpitbhayani.me/blogs/monotonic-reads)
* Distributed key-value store with one primary and two replica nodes
* Three updates to key `X`
  * First update (`U1`) sets `X` to `1`
  * Second update (`U2`) sets `X` to `2`
  * Third update (`U3`) sets `X` to `3`
* Writes go to primary node and are asynchronously propagated to replicas
* Reads go to any replica randomly
* Replicas will have replication lag, and this replication lag might not be consistent between the replicas
  * So Replica 1 could have a lag of 2 seconds while Replica 2 has a lag of 1 second
  * In this scenario, read requests could oscillate between more recent values and older values depending on which replica a read is routed to
  * So a read for key `X` could produce `2` if it goes to Replica 2 but then the same read could produce `1` if it was routed to Replica 1 (which is even more behind and is still yet to process `U2`)
    * And then the same read could produce `2` if it goes to Replica 3 again
  * Monotonic reads guarantees users that they will see values always moving forward in time, no matter how many times they attempt to read the data
    * Weaker guarantees than strong consistency but a stronger guarantee than eventual consistency
  * Make read requests from a specific user always go to the same replica
    * Hash the requester's user ID to identify the appropriate replica
