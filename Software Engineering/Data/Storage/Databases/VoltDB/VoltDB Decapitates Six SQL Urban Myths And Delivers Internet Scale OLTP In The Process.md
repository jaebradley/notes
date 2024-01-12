# [VoltDB Decapitates Six SQL Urban Myths And Delivers Internet Scale OLTP In The Process](http://highscalability.com/blog/2010/6/28/voltdb-decapitates-six-sql-urban-myths-and-delivers-internet.html)

## Features

* All data is stored in RAM
  * No buffer pools to manage
  * No blocking disk stalls
* Single-threaded transaction execution
  * Single-threading removes all locking overhead
  * Allocate memory to each CPU on a multi-core system
    * Each CPU runs in a single thread
* Replication
  * No logs, no disks
  * Active-active architecture for high availability
    * Each transaction is run consecutively on each active node
    * Replicas are ACID consistent
  * Data can be asynchronously stored on disk - 5% performance hit
* The unit of a transaction is a stored procedure
  * Transactions cannot span multiple rounds of communication with a client
  * Almost all stored procedures / transactions must be single partition
    * If data is partitioned by graph node
    * Updating multiple nodes in a single transaction / stored procedure will not be a single partition transaction
* Design schema and workflow to use single-sited procedures
  * Table data is stored in partitions
  * Partitions are split onto different nodes
  * When a query can run on a single node, this will produce the best performance
* VoltDB is only for OLTP (online transaction processing)
  * It should not be used for OLAP (online analytical processing)
  * To support OLAP, could replicate updates that are delayed relative to the main transaction system into an analytics-specific RDBMS (like Vertica)
    * Idea is that one size does not fit all
    * "You should run transactions on something that is good at transactions and run reporting on something that is good at reporting"
* Architected to remove the four most common sources of overhead - logging, latching, locking, b-tree and buffer management
* Argument that in-memory databases are a waste of RAM and that only hot data should be kept in RAM
  * Counter-argument is that RAM can hold the entire data set so why compromise
* Should not compare against other persistence layers like Cassandra as they have different use-cases
  * Cassandra was built to store petabytes of data, across hundreds of nodes, in multiple data centers, with the ability to scale up (i.e. add nodes) easily

## The Six SQL Urban Myths

### Myth #1A: SQL Is Too Slow Because Of Heavy Interfaces Like ODBC/JDBC

* Interfaces like ODBC and JDBC cause too many round trips to the database
* VoltDB avoids this problem by using stored procedures
* A big advantage that stored procedures have over ODBC/JDBC protocols is that one message is sent to the database and one reply is sent back

#### Downside to Stored Procedures

* Difficult to prgraom, ugly to use, hard to debug
* Putting logic into the database makes the database an application server
  * If a stored procedure needs to make an API call, introduces blocking, I/O, etc. except database servers aren't optimized for such operations
  * VoltDB does not allow these operations
* Once the database can't scale, you're dead
  * Can't really make it perform better
  * People separate out logic from data and let each scale independently
* Can take half a second to several seconds to prepare a statement in VoltDB so ad-hoc queries are not practical
* VoltDB does not allow SQL for getting the schema of tables, listing tables, altering tables

### Myth #1B: SQL Is Too Slow Because SQL Engine Implementations Have Too Much Overhead

* Typical OLTP databases have to support "work" like managing disk buffer pools, crash recovery

### Myth #2: I like a K-V interface, so SQL is a non-starter 

* VoltDB creates a thin get/put layer on top of SQL using stored procedures

### Myth #5: ACID Is Too Slow, So Avoid Using It

* Implementing logical transactions (i.e. implementing transactions in user code) is the worst of all worlds
* 99% of OLTP databases are 1TB or less, so most transactional databases can fit in main memory

### Myth #6: In CAP, Choose AP Over CA

* Network partitions happen, so you have to choose between consistency or availability
* Order matters
  * Not all transactions are commutative
    * Order doesn't matter for transactions that add/multiply values
    * Order matters for non-commutative operations lik subtraction/division
      * 4 - 7 != 7 - 4 (i.e. the order of the values matters)
  * Eventual consistency will give you the wrong result
* Real-world constraints
  * Example is stock for an item, and don't want to send an item if no stock is left
  * In an eventually consistent system, if there's a partition, can't support this as each partition does not know what the other is doing
  * Need ACID if semantic constraints exist
* Error rates
  * Human mistakes are the biggest source of errors
  * Network failures are rare(r) than other sources of errors
  * So designing a system for the rarest failure case is a bad tradeoff
