# [Notes on the FoundationDB Paper](https://blog.the-pans.com/notes-on-the-foundationdb-paper/)

## Control Plane

* Most important service is the Coordinator
* It stores small metadata about the entire FDB deployment and configuration
* ClusterController monitors the health of all the servers

## Data Plane

* Transaction System, Log System, Storage System
* Log System = distributed log
* Storage System 1st disaggregated, shared storage

## Transaction Management

* Client connects to Proxy
* Proxy communicates with FDB “internal” services
* There is only one physical Sequencer in the system
* Sequencer serves as single point of ordering (serialization)
* Conceptually it produces monotonically increasing Hybrid Logic Clock
* Client issues reads to the Storage System with the specific read-version, acquired from the Sequencer
* Storage system returns data for the specific point-in-time indicated by the read-version
* Client asks Sequencer for the commit-version
* Client sends the read-version and the commit-version to the Resolver
* Resolvers are range-sharded and keep a short history of recent writes
* Resolvers detect if there data read earlier between between a read-version and a commit-version has changed
* If no conflict is detected, commit the writes to the Log System with the commit-version
* Once the logs have been ACK-ed enough, the Proxy returns success to the client
* Effectively, Optimistic Concurrency Control
* If conflicts exist, i.e. what the client read has changed, the transaction will be aborted or retried

### Proxy

* Sits between clients and the transaction system
* Semi-stateful “client”
* Performs batching of requests from multiple clients for reducing Sequencer queries

### Resolvers

* Proxy needs to wait to hear back from all Resolvers involved in each transaction
* Resolvers do not get recently committed keys, as that would require a distributed transaction between the Log and the Resolver
* Resolvers keep a set of recent writes-attempts (not commits), and their versions
* It is possible that a key appears to be recently written in a Resolver, while in fact, its transaction was rolled back, leading to false positives
* Transaction with read version Vr, commit version Vc
* Second transaction with commit version Vc2, which is between Vr and Vc hits the Resolver
* Log servers make sure to accept Vc2 first before accepting Vc, preserving linearlizability

## Logging Protocol

* Multiple LogServers
* FDB provides an abstraction of one non-sharded database
* FDB decouples sequencing from logging
* Single Sequencer, mulitple logs
* Logs don’t have to perform the job of ordering, like in other systems
* Unlike a system like Raft, which couples sequencing and logging, there is less of a performance cost when decoupling sequencing and logging
* The FDB Sequencer is physically a singleton - there’s a single process
* Proxy broadcasts a message to all LogServers
* Each message has a header that contains the LSN (current TX ID), previousLSN, and KCV
* KCV is the Proxy’s last known committed version
* KCV should always be less than the LSN (current transaction ID)
* KCV is an incomplete local view of the world, a view that will be used later in the recovery process
* Not all broadcast messages include data
* Key `A` is stored on storage shard `S`
* A Coordinator maintains an affinity mapping between a storage server and its preferred LogServer
* Data will only be included in the message sent to shard `S`’s preferred LogServer (and potentially other LogServers depending on replication requirements)
* All other messages to other LogServers will only include the common header
* Once the data is logged, it is then applied to the storage node
* All logs get all messages because messages are applied in order on each log - there are no gaps
* Storage Node Count > Log Server Count > Sequencer Count (1)
* Sequencer is much more lightweight than Log Servers since they don’t write to disk
* Log Server can support multiple storage nodes as logs are cheaper than storage

## Recovery Process

* Failures detected by the ClusterController, via heartbeats
* Sequencer locks and reads the previous states from Coordinators
* Could be a new Sequencer instance if the previous Sequencer is in a faulty state
* Lock coordinators so multiple Sequencers don’t make changes
* Recovery Sequencer stops old logs from accepting new transactions
* Contacting old LogServers to discover end of the redo log (last commit LSN)
* Sequencer recruits new Proxies, Resolvers, and LogServers
* Writes the new system state to Coordinators, releases lock
* Sequencer can now accept transactions
* FDB’s recovery process has downtime, from the shutdown of old LogServers to when the new Sequencer starts accepting new transactions
* P50 downtime of FDB is ~3s
* No one can write to FDB during this window
* Clients can still read from snapshots, but cannot execute read-only transactions
* That being said, most production read worklaods are probably snapshot reads vs. read-only transactions
* Proxies are easy to recover as they are mostly stateless (just keep track of a KCV, locally)
* Resolvers are stateful, but it’s safe to discard all Resolver data between restarts (from the previous epoch)

### Log Recovery

* Each log maintains a durable version, the max persisted LSN
* Each log also maintains a known last committed version, the last known committed version from the Proxy
* Both of these values are on every message a Proxy sends to the logs
* Sequencer attempts to stop all logs
* After hearing back from # hosts - replication factor + 1 logs, Sequencer knows that previous epoch has committed transactions up to the max known last committed versions from all the logs
* New Sequencer will start its epoch from the max last known committed version + 1
* Recovery Version value is the minimum of the durable versions (the max persisted LSN)
* Data is copied from the previous epoch’s end version and the Recovery Version from the old logs to the new logs, then discarding logs after the recovery version
* This process allows data preservation for data that is committed with an LSN > previous epoch end version

### Proof of Correctness

* Any log messages arriving at the log server have already passed conflict detection - they are safe to be durably stored
* Err on the side of preserving more of the old logs except when its not safe to do so
* Know for sure all transactions before previous epoch’s end version were committed
* Need to handle partial failures that might be happening, for which there must be a rollback, and tail data that no clients have seen yet
* In order to safely discard data after the Recovery Version value, and preserve data before the Recovery Version (which is a Durable Version value), this Durable Version cross-range-shard transaction cannot be partially committed
* No client can read any data written after the Recovery Version value

#### Proof by Contradiction

**A cross-range-shard Recovery Version transaction cannot be partially committed**

* Cross-range-shard transaction: a transaction that sets both key A and key B
* “Partial” means that Key A is stored with data in its logs but Key B is not
* Have to rollback A
* Logs can tolerate replication factor - 1 failures
* If one surviving Key B replica has data for the transaction, then no rollback is required, and the commit will finish
* But if k - 1 Key B replica crashed there is one Key B replica that is running and has not received this transaction’s message
* For this replica, it’s logged durable version must be smaller than the latest transaction’s version - this is a contradiction

**No clients can read any data written after the Recovery Version**

* For each log, the Durable Version >= Known Committed Version because the LSN > Known Committed Version for each Proxy
* Local Log Durable Versions can sometimes be the same as the Known Committed Version because Proxy reads don’t set the Durable Version, but can bring Local Log Known Committed Versions up-to-date
* max(Known Committed Versions) <= min(Durable Versions) should still be true since each log commit requires broadcasting message to all logs
* FDB only serves reads base don Known Committed Versions (and not Durable Versions)
* For any Durable Version > Recovery Version, those transactions must not be committed

**Can treate Recovery Version as tail of redo log**

* Presence of a Known Committed Version on a log implies that the associated transaction was fully replicated (i.e. has replication factor copies)
* Conceptually, these messages have been “durably flushed” to storage nodes and can be safely discarded from the logs
* For messages between the previous epoch version + 1 and the Recovery Version, they might have been commited
* Don’t know if these messages have been durably stored in the storage nodes or not, so start new epoch at previous epoch version + 1 and replay messages from PEV + 1 to the recovery version

