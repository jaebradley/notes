# [Sharding](https://vitess.io/docs/23.0/reference/features/sharding/)
* Vitess keyspace can be unsharded or sharded
* Unsharded keyspaces = a single MySQL database
* Sharded keyspaces mean that the rows of the keyspace is partitioned into different databases with identical schemas
* Vitess shards typically contain one MySQL primary and many MySQL replicas
  * Primaries handle writes while replicas handle read-only traffic, batch processing operations
  * Each MySQL instance within a shard should have the same data, except when there is replication lag
* Horizontal sharding is splitting / merging shards across a sharded keyspace
* Vitess calculates the sharding key(s) for each query, and then routes the query to the appropriate shards
* Vitess uses key ranges to determine which shards should handle any particular query
  * Key range is a series of consecutive keyspace ID values
  * A key falls inside the range if the key is >= the start value of the range and strictly less than the end value of the range
* A partition is a set of key ranges that covers the entire key space
