# [Redis replication](https://redis.io/docs/latest/operate/oss_and_stack/management/replication/)
* When the primary and replica instance are connected, the primary keeps the replica updated by sending streams of commands to the replica
* After a disconnection, the replica reconnects and attempts to partial resynchronize and obtain the stream of commands missed during this disconnection
* If a partial resynchronization is not possible, the replica will ask for a full resynchronization
  * Primary needs to create a snapshot of all the data, send it to the replica instance, and then continue sending a a stream of commands as the dataset changes
* Redis replication does not block on the primary instance
  * The primary instance will continue to process queries when a replica is performing a full or partial synchronization
* A full resynchronization will block incoming connections on the Redis replica
  * The old dataset must be deleted and the new one must be loaded
  * While old dataset deletion can occur in a non-main thread, loading the new dataset still happens in the main thread and will block the replica
* Every primary instance has a replication ID, a large pseudo-random string that represents a specific history of the dataset
* Every primary instance also has an offset that increments for every byte of replication stream that is produced for use by the replica instances
  * The combination of a replication ID and an offset identifies an exact version of the primary instance's dataset
* When replicas connect to a primary instance, they use the `PSYNC` command to send their old primary replication ID and offset
  * If possible, the primary can send the difference from the primary's replication ID and offset to the replica
  * There are two cases where a partial resynchronization is not possible and full resynchronization occurs: 1. the primary instance's backlog buffer does not contain enough history or 2. the replica is referring to a history (replication ID) that is not known
* Full synchronization: primary instance starts a background process to produce a RDB file (snapshot)
  * Primary instance starts to buffer all new write commands
  * When the background saving process completes, the primary transfers the database file to the replica
  * Replica instance saves the database file to disk and loads the file to memory
  * Primary instance then sends all buffered commands to the replica instance
* Every time an instance becomes a primary instance (via start or promotion) a new replication ID is generated
  * When a replica connects to a primary, it will inherit its replication ID
* If two instances have the same replication ID, but the first instance has an offset of `1000` and the second instance has an offset of `1023`  means that the first instance lacks certain commands applied to the data set
* Instances actually have two replication IDs, in case a replica is promoted to a primary
  * After a failover, the promoted replica needs to remember what its previous replication ID was
  * When other replicas sync with the new primary, they will use the previous primary's replication ID
  * This will work as expected because when the replica is promoted to primary, it sets its secondary ID as its main ID
  * The promoted primary will select a new random replication ID
  * When handling new replicas connecting, the primary will match the replica IDs and offsets with both the current and the secondary ID (up to a given offset)
  * After failover, replicas connecting to a newly promoted primary don't have to perform a full sync
  * A replica promoted to primary needs to change its replication ID after a failover because the old primary instance could still be working as a primary instance due to a network partition
    * Retaining the same replication ID + offset would violate the fact that the same ID and same offset of any two random instances means they have the same data set
