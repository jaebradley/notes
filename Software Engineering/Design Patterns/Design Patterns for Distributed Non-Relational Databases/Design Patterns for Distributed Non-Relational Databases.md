# [Design Patterns for Distributed Non-Relational Databases](http://static.last.fm/johan/nosql-20090611/intro_nosql.pdf)

* Scaling horizontally with traditional RDBMS isn't ideal
  * Lose all features that make RDBMS useful when sharding
  * Sharding also adds operational and management overhead
* Data assumptions
  * Data does not fit on one node
  * Data may not fit on one rack
* Reliability assumptions
  * Must be highly available to serve other applications
  * Has to be able to handle node failure gracefully
  * Data must be safe though disk and node failure
  * System must replicate each row to multiple nodes
* Performance assumptions
  * Systems must be able to handle real-time use
  * 99th percentile latency is more important than average latency
  * Must operate on commodity hardware and slow disks

## Design Patterns

### Partitioning Schemes

* Given a key, need to determine which node(s) it belongs on
* If node is unavailable, need to find replica

### Consistent Hashing

* Example - key: `user:tlipcon`
* MD5 hash the key = `40b21`
* Find first node with hash value less than / greater than (depending on node resolution strategy) hashed value

### Consistency Models

* Consistency model determines rules for the visibility of updates and the apparent order of updates

### Strict Consistency

* All read operations must return the data from the latest completed write operation, regardless which replica the operations went to
* Implies that all operations for a given row go to the same node
* OR that nodes employ a distributed transaction protocol

### Eventual Consistency

* With infinite time, readers will see writes
* System is guaranteed to eventually return the last written value
* Read-your-own-writes consistency - "sent mail" box
* Causal consistency (if you write Y after reading X, anyone who reads Y sees X)

### Timestamps and Vector Clocks

* Eventual consistency relies on deciding what value a row will eventually converge to
* Using timestamps relies on synchronized clocks, and doesn't necessarily capture causality

### Vector Clocks

* A vector clock is a tuple of clock values from each node
* Some value (V1) can be said to be written before another value (V2) if
  * For all nodes, V1 <= V2
  * AND for at least one node, V1 < V2
* When data is written to node i, vector clock sets the clock value for node i to the vector clock's current clock value

### Multi-Version Storage

* Each table cell has a timestamp
* Multiple versions can exist concurrently for a given row
* Reads may return "most recent" or "most recent before T"

### Row-based Storage

```
| Record 1 |
| c1|c2|c3 |
| Record 2 |
| c4|c5|c6 |
| Record 3 |
| c7|c8|c9 |
```

* Locality of access on disk and in-memory of different columns
* Reading/writing single row is a single I/O operation
* Scanning a single column involves scanning all rows

### Columnar Storage

```
| Column 1 |
| c1|c2|c3 |
| Column 2 |
| c4|c5|c6 |
| Column 3 |
| c7|c8|c9 |
```

* Data for a given column is stored sequentially
* Scanning a single column (like executing aggregation queries) is fast
* Reading a single row may seek once per column

### Columnar Storage with Locality Groups

```
| Column 1 | <-- "Locality Group 1"
| c1|c2|c3 | <-- One row
| Column 2 & Column 3 | <-- "Column Family" comprised of Column 2 and Column 3
| c4|c7|c5|c8|c6|c9| <-- Cell values are row-based within a "Column Family" from Column 2 and Column 3
```

* Columns are organized into families / locality groups
* Row-based layout within a group
* Can only read the locality groups you care about

### Log-Structured Merge Trees (Bigtable Model)

* Premise is that random I/O for writes is bad
* LSM trees convert random writes into sequential writes
* Writes go to a commit log and in-memory storage (Memtable)
* The Memtable is occasionally flushed to disk (SSTable)
* The disk stores are periodically compacted

### Omniscient Leader

* When nodes join/leave they talk to the leader
* The leader node holds the authoritative view of the world
* Benefits is that there is a single consistent view of the cluster
* Downsides is that the leader is a potential single point of failure unless the leader is made highly available

### Gossip

* Gossip is a method to propagate a view of cluster status
* Every t seconds
  * One node selects another node to chat with
  * Node reconciles view of cluster with its gossip buddy
  * Each node maintains a timestamp for itself and for the most recent information it has from every other node
* Eventual consistency - information spreads in O(log # of nodes) rounds
