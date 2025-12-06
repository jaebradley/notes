# [Multi-Master Replication - Scaling Writes Across Geographies](https://arpitbhayani.me/blogs/multi-master-replication)
* Multi-primary setup is multiple nodes in a database cluster that accept writes
* Client can pick primary node or a patch picks one
* Write propagation is generally asynchronously and data becomes eventually consistent
* Each primary node can form a sub-cluster of read-replicas to help handle incoming read requests
* Multi-primary setups prevent single point of failure associated with a single primary node handling all writes
* By distributing primary nodes across the world, write latencies decrease by not forcing writes to go to the one region where the primary node resides (in the case of a single primary node setup)
* Facilitates database upgrades by allowing incremental traffic to flow to the new primary node with the upgraded database version
* A relational database running in a multi-primary setup loses ACID guarantees (due to asynchronous replication)
* Write-heavy activity that leads to replication messages to many primary nodes can saturate the network
* Due to asynchronous replication, conflicts can happen. Is the entire sequence of writes discarded? Does the last write win?
