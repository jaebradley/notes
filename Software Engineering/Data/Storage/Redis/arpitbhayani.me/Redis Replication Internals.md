# [Redis Replication Internals](https://arpitbhayani.me/blogs/redis-replication/)
* Primary Redis instance actively sends data to replica instances rather than replica instances polling the primary instance for updates
* When a write command is executed on the primary Redis instance, Redis propagates that command to all connected replicas immediately
  * Replica sends a `PSYNC` command to the master instance
  * If this is the first sync or if the replica is too far behind, the primary instance performs a full resync
    * Primary instance creates a database snapshot in the background
    * Primary instance buffers all new writes during snapshot creation
    * Primary instance sends the database snapshot file to the replica instance
    * Primary instance sends buffered writes to the replica
  * After the initial sync, the primary instance enters push mode
    * Every write command executed on the primary instance is sent to all replica instances
    * Enabled by the replication backlog buffer
    * Replica instances execute the commands in the same order
* Replication backlog is a circular buffer that the primary instance maintains
  * Stores a recent history of write commands (defaults to 1MB)
  * If a replica disconnects briefly, the replica can resume from where it left off
  * Cushion when replicas temporarily fall behind
  * Backlog is a push-oriented data structure - the primary instance continuously appends to it and pushes offsets to replicas
* Redis is designed for low-latency (microsecond) operations, so it is push-based vs. pull-based
  * A pull-based approach would have replication lag due to polling interval: replica instances would need to wait for the next polling cycle
* Push can be more network-efficient
  * Network traffic only occurs when writes actually occur
  * In a pull-based system, replicas must regularly poll, even when there are no updates
* Redis's core design is single-threaded for command execution
  * Command execution, pushing to the replication backlog, and then pushing operations to connected replicas can happen atomically within the same event loop
* The primary instance control replicas and monitor each replica's output buffer
  * The primary instance can disconnect slow replicas to protect itself
  * In a pull-based model, the burden would be on replicas to manage their own rate of consumption
    * Replicas wouldn't have visibility into the primary's state or their current state relative to other replicas
* Failure modes
  * Replica instance disconnection: primary instance immediately know via TCP connection
    * The primary can stop pushing to the replica, freeing resources
  * Primary instance failure: replicas should be in-sync with the latest received command
    * Elect the most up-to-date replica and promote that replica
  * Network partition: the primary instance can refuse writes if too many replicas are unreachable or fall behind to prevent split-brain scenarios
    * There are two configuration variables where the primary instance can refuse writes if it doesn't have at least X replicas with Y seconds of lag
* Primary instance must maintain 
  * TCP connections to all replicas
  * Output buffers for each replica
  * Replication backlog
  * A primary with a large number of replica instances can consume significant memory
* Scaling patterns when reading data
  * Clients can read from nearby replicas for latency-sensitive operations
  * The `WAIT` command can be used to enforce stronger consistency
    * `WAIT 1 1000` blocks until at least one replica acknowledges a write (with a second timeout)
  * Redis replication is asynchronous and there is no distributed consensus
    * So reading from replicas may return stale data
    * For critical reads, read from the primary instance or use `WAIT`
