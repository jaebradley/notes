# [Partitioning Data - Range, Hash, and When to Use Them](https://arpitbhayani.me/blogs/some-data-partitioning-strategies-for-distributed-data-stores)
## Range-based Partitioning
* Example is a key/value store distributing partitions across keys starting with `a-e`, `f-k`, etc
* Goal is to distribute the load across partition nodes, so the partition *range*s may not be uniformly distributed
* Classic case when range-based partitioning fails is partitioning time-series data by day
  * The last day's partition will most likely be "hot" since writes (and most likely reads) will go to the same partition

## Hash-based Partitioning
* Use a hash function that distributes keys equally over a given range
* Each partition owns a set of hashes over the range
* When supporting a hash-based partitioned key/value store, the lookup key is hashed to identify the correct partition, and then an additional lookup occurs on the partition for the specified lookup key
* Hash-based partitioning is not ideal for range queries
  * Data is unordered and scattered across partitions so in order to support range queries, all partitions may need to be visited
* Range queries are doable when the required range lies on one partition
  * DynamoDB specifies a partition / hash key and a range key
  * Data is stored across multiple partitions and is partitioned by the hash key
  * Within a given partition, data is ordered by range key so that range queries local to the specified partition can be executed somewhat efficiently
