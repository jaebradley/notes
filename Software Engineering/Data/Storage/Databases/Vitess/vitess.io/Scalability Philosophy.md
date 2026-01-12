# [Scalability Philosophy](https://vitess.io/docs/22.0/overview/scalability-philosophy/)
* Vitess recommends turning on semi-sync replication
  * Allows Vitess to failover to a new replica when a primary goes down, with no data loss
  * Vitess recommends creating a new database from a recent backup and letting it catch up
* Cross-shard reads may not be consistent so how to shard should minimize these cross-shard occurrences
* Cross-shard transactions can fail in the middle and result in partial commits
  * "Two-part commit mode" transactions give distributed atomic guarantees but increase the write cost by 50%
* VTGate servers are capable of monitoring replica lag and can be configured to avoid serving data from instances that are lagging more than X seconds
* Consistency levels:
  * Replica: Servers can be scaled geographically and local reads are fast, but may be stale depending on replica lag
  * Primary: One worldwide primary per shard
    * Reads coming from remote locations will be subject to network latency and reliability, but the data will have read-after-write consistency
    * Transactions will be ACID for a single shard
* Vitess does not support active-active replication
  * Vitess argues that since all writes have to eventually be applied to all writable servers, this does not scale horizontally, like sharding
* Vitess recommends absorbing the latency cost of long-distance round-trips for writes
  * Can shard based on geographic affinity
  * Can setup primaries for different shards in different geographic locations so that most writes can still be local
* Vitess is meant to be run across multiple data centers / regions
  * Vitess uses the term "cell" to reference a set of servers that are geographically located close together and share the same regional availability
  * Cell typically consists of a set of tablets, a vtgate pool, and application servers that use the Vitess cluster
  * It is also possible to have some shards with a primary in one cell and some other shards with their primary in another cell
    * vtgate will route traffic to the right place, incurring extra latency cost only on the remote access
    * Example: creating US user records in a database with primaries in the US and creating European user records in a database with primaries in Europe
