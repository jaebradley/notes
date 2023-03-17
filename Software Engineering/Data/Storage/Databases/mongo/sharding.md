# [Sharding](https://docs.mongodb.com/manual/sharding/)

* A `shard` contains a subset of the data
* `mongos` acts as a query router, the interface between the client application and the sharded cluster
* `config servers` store metadata and configuration settings for the cluster

## Shard keys

* The shard key, an immutable field or field, are used to partition documents in a collection
* They must exist in every document in the target collection
* The shard key cannot be changed after sharding
* A collection can only have __one__ shard key
* A collection must have an index that starts with the shard key
* Sharded collections are partitioned and distributed across the shards in the cluster - unsharded collections are stored on a primary shard

## Advantages from Sharding

* When data is shared across shards, each shard is able to process a subset of cluster operations
* For queries that include the shard key or the prefix of a compound shard key, mongos can target the query at a specific shard or set of shards
  * These targeted operations are generally more efficient than broadcasting to every shard in the cluster
* Cluster can still perform partial read/write operations even if shards are unavailable
  * While the subset of data on the unavailable shards cannot be accessed during the downtime, reads or writes directed at the available shards can still succeed
* In production, individual shards should be deployed as replica sets, providing increased redundancy and availability

## Sharding Stategy

### Hashing

* A hash is computed using the shard key field's value
  * Each chunk is assigned a range based on the hashed shard key values
* MongoDB automatically computes the hashes when resolving queries using hashed indices - applications do not need to compute hashes
* This implies that range-based queries using the shard key are less likely to target a single shard, resulting in more cluster-wide broadcast operations

## Ranged

* Dividing data into ranges based on the shard key values
  * Each chunk is assigned a range
  * Thus, values that are "close" are more likely to reside on the same chunk
  * This allows for targeted operations as `mongos` can route operations to only the shards that contain the required data
