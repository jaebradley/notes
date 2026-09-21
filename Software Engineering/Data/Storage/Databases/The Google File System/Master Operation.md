# Master Operation
* The primary executes all namespace operations
* Manages chunk replicas, balances load across chunk servers, and reclaims unused storage

## Namespace Management and Locking
* Unlike many traditional file systems, GFS does not have a per-directory data structure that lists all files in that directory
* It does not support aliases for the same file or directory (i.e. hard or symbolic links)
* GFS logically represents its namespace as a lookup table mapping full pathnames to metadata
* With prefix compression, this table can be efficiently represented in memory
* Each node in the namespace tree (absolute file name or an absolute directory name) has an associated read-write lock
* Each primary operation acquires a set of locks before it executes
* If an operation involves `/d1/d2/.../dn/leaf` the operation will acquire read-locks on directories `/d1`, `/d1/d2`, `/d1/d2/.../dn`
  * Will most likely involve acquiring a read lock or write lock on `/d1/d2/.../dn/leaf` where `leaf` may be a file or directory depending on the operation
* Example where `/home/user` is snapshotted to `/save/user`
  * Snapshot operation acquires read locks on `/home` and `/save`
  * Also acquires write locks on `/home/user` and `/save/user`
  * There is a concurrent file creation operation for `/home/user/foo`
    * This file creation operation acquires read locks on `/home` and `/home/user`
    * Acquires write lock on `/home/user/foo`
  * The operations will be serialized properly because they try to obtain different locks on `/home/user`
    * File creation does not require a write lock on the parent directory because there is no "directory" or inode-like data structure to protect against modification
    * Read lock on the file name is sufficient to protect the parent directory from deletion
* Multiple file creations can be executed concurrently in the same directory
  * Each file creation operation acquires a read lock on the directory name and write lock on the file name
  * The read lock is sufficient to prevent the directory from being deleted, renamed, or snapshotted
  * The write locks on the file names serialize attempts to create a file with the same name twice
* Read-write locks are allocated lazily and deleted once they are not in use
* Locks are acquired in a consistent total order to prevent deadlock
  * Ordered by level in the namespace tree and lexicographically within the same level

## Replica Placement
* A GFS cluster typical consists of hundreds of chunkservers spread across many machine racks
* Chunkservers are accessed from hundreds of clients from the same or different server racks
* Communication between two machines on different racks may cross one or more network switches
* It is not enough to spread replicas across machines, which only guards against disk or machine failures
  * This does fully utilize each machine's network bandwidth
* Must also spread chunk replicas across racks ensuring that some replicas of a chunk survive and remain available even if an entire rack is damaged or offline

## Creation, Re-replication, Rebalancing
* When the primary creates a chunk, it chooses where to place the initially empty replicas
* Want to place new replicas on chunkservers with below-average disk space utilization
* Want to limit the number of recently created chunks on each chunkserver
  * While chunk creation is cheap, it reliably predicts imminent heavy write traffic
  * Chunks become practically read-only once they've been completely written
* Want to spread replicas of a chunk across racks
* Primary re-replicates chunks as soon as the number of available replicas falls behind some user-defined target
* Chunks that have lost two replicas vs. a single replica will be given higher re-replication priority
* Chunks that belong to live files vs. recently deleted files will be given higher re-replication priority
* Chunks that are blocking any client progress are also given higher re-replication priority
* The primary rebalances replicas periodicially by examining the current replica distribution and moving replicas for better disk space and load balancing

