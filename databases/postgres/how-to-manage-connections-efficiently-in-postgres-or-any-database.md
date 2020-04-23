# [How To Manage Connections Efficiently In Postgres, Or Any Database](https://brandur.org/postgres-connections)

* Postgres `max_connections_key` defaults to `100`
* Postgres has a central Postmater that accepts incoming connections and forks child processes to handle them
  * Each process is `~5MB` but can grow based on data it's accessing
  * Bottleneck isn't the absolute memory ceiling but that the Postmaster and backend processes use shared memory for communication
  * For example, there are various operations like getting snapshot data that needs to loop through every process in the system
  * Performance is actually proportional to the number of active connections
* Connection pool is a cache of database connections
  * Overhead to opening new database connection for both client and server
  * After finishing using connection, instead of discarding it, can be inserted back into pool and reused next time it's needed in application
  * Ensure that connections are released when an application is making requests to other APIs (for example) so that connection utilization is even more efficient while (slow) network I/O is occurring
    * Though application should not be in a transaction when mutating when talking to other APIs
* If there are `N` nodes and `M` max connections per node, `N x M` may be greater than database's max connections to guard against case where a single node gets a larger-than-expected amount of work and needs more connections
* PgBouncer acts as a global pool by proxying all connections (basically, global connection pool)
  * Session pooling is when a connection is assigned to a client on open and unassigned on close
  * Transaction pooling is when connections are assigned only for duraction of transaction
    * Applications cannot use features that change the state of connection like `SET` / `LISTEN` or prepared statements
* It's common to hit limits during a deploy because a graceful restart spins up new workers or nodes before shutting down old ones so know expected connection numbers during deployments as well