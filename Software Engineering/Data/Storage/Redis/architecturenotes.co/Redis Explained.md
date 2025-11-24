# [Redis Explained](https://architecturenotes.co/p/redis)
* Redis is a data structure server
* Primarily, Redis is used as an in-memory database used as a cache in front of another "real" database (MySQL, PostgreSQL)
* Memcached is multithreaded while Redis is single-threaded

# Single Redis Instance
* Given enough system resources, a Redis service could be deployed on the same server the application is running on
* Commands sent to Redis are processed in-memory
* If persistence is configured on these Redis instances, a forked process (on some interval) facilitates data persistence
  * Redis database snapshots or append-only files
* If Redis isn't configured for data persistence, data will be lost on restart or failover

## Redis High Availability
* Primary and secondary instances with replication keeping the secondary instance in sync with the primary instance
* As data is written to the primary instance, the primary instance sends copies of these commands to an output buffer
* This output buffer sends commands to secondary instances to replicate

## Redis Replication
* Every primary Redis instance as a replication ID and an offset
  * These two pieces of data are critical for identifying the point in time state of the Redis database
  * In other words, if an instance has the same replication ID and offset, they have precisely the same data
* Offset is used to determine whether to execute a partial synchronization or a full synchronization
* Full synchronization involves a primary instance creating a Redis Database snapshot and sending it to the replica
  * As this is happening the primary instance is buffering any intermediate updates to send to the replica once the full synchronization completes

## Redis Sentinel
* Cluster of sentinel processes that work together to coordinate state
* Sentinel ensures that current primary and secondary instances are functioning
* Sentinel processes can alert and act on situations where the primary and/or secondary nodes are lost
* Sentinel serves a role in service discovery - when a new client attempts to write to Redis, Sentinel will tell the client what the current primary instance is
* If there is a network partition and the old primary instance is in the minority group during quorum election, any writes to that primary instance are lost when the system recovers
* Forcing the main instance to replicate writes to a minimum of one secondary instance can help mitigate data loss when a new primary is elected

## Redis Cluster
* Redis Cluster uses hash slots to help with resharding
* There are 16k hash slots and all data is mapped to one of these hash slots
* Two instance configuration: machine 1 owns hash slots 0-8191 and machine 2 owns hash slots from 8192-16383
* Adding a new instance, machine 3, means that machine 1 owns hash slots 0-5460, and machine 2 owns hash slots 5461-10922, and machine 3 owns hash slots from 10923-16383
* Some keys that were mapped to hash slots on machine 1 will now need to be mapped to hash slots on machine 2
* Redis Cluster uses gossiping to determine the cluster's health
* If enough nodes agree that a primary instance isn't responsive, they can decide to promote the primary's secondary instance as a primary
* Have an odd number of primary nodes and two replicas for each primary

## Forking
* Redis is a process with tons of memory allocated to it
* A forked child process shares memory with the parent
* If the child process edits shared files, the kernel keeps track of references to each of the edited pages (copy-on-write)
