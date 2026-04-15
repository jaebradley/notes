# [Vindexes](https://planetscale.com/docs/vitess/sharding/vindexes)
* Vindex maps incoming rows of data to the appropriate shard in the keyspace
* Primary Vindex is the Vindex that determines which shard each row of data resides on
* Secondary Vindices are created to help speed up queries in a sharded environment

## Functional Vindex
* Executes a function on 1+ column values from a row that produces a 64 bit number, as a keyspace ID
* Each time a row is inserted into the table, Vitess extracts the relevant column values and calculates this keyspace ID
* The keyspace ID tells Vitess which shard the row should be saved to

## Keyspace IDs
* “keyspace ID” is an identifier used to determine which shard a row should be stored on *within* a keyspace
* When a single keyspace contains multiple shards, each shard follows sharding naming conventions like
  * `BEGINNING_ID-ENDING_ID`
  * First shard has an empty beginning ID and the last shard has an empty ending ID

## Primary Vindexes
* Example is an `exercise_log` table
```
+-------------+-----------------+------+-----+---------+----------------+
| Field       | Type            | Null | Key | Default | Extra          |
+-------------+-----------------+------+-----+---------+----------------+
| log_id      | bigint unsigned | NO   | PRI | <null>  | auto_increment |
| user_id     | bigint unsigned | NO   |     | <null>  |                |
| exercise_id | bigint unsigned | NO   |     | <null>  |                |
| gym_id      | bigint unsigned | NO   |     | <null>  |                |
| reps        | smallint        | YES  |     | <null>  |                |
| created_at  | datetime        | YES  |     | <null>  |                |
| edited_at   | datetime        | YES  |     | <null>  |                |
| deleted_at  | datetime        | YES  |     | <null>  |                |
| notes       | varchar(1024)   | YES  |     | <null>  |                |
+-------------+-----------------+------+-----+---------+----------------+
```
* Can’t use an auto incrementing primary key in a sharded keyspace, and instead need to use a sequence table
* If the `log_id` column was used as the primary Vindex, for each row inserted into the table, the `log_id` value would be hashed and assigned to a shard
  * All the rows would be evenly distributed across all shards, which is good for insert performance
  * However, most queries will be to fetch a sequence of exercise logs for a given user
  * If the logs are randomly distributed across all shards, then when fetching the logs for a given user, all shards would have to be searched for a given user
  * Instead, use the `user_id` as the primary Vindex so that all logs for a given user will end up on the same shard

## Secondary Vindexes
* Example is a query to get all the `exercise_log` rows for a specific gym
* There’s no filtering by the primary Vindex, just `gym_id`
* Vitess must do a full shard scan and aggregate the results
* Create a lookup table in MySQL to store the mapping between `gym_id`s and `user_id`s (which are the keyspace IDs)

## Built-in Functional Vindexes
* `hash` uses the DES hashing algorithm
* `xxhash` uses the `xxhash64` algorithm
* DES was designed to be cryptographically secure, and unless this is a requirement for the keyspace ID, `xxhash` should be used as it has better performance
* `numeric` is an identity function for column values that are already good keyspace ID chocies
* `consistent_lookup` and `consistent_lookup_unique` are preferred over `lookup` and `lookup_unique` as they do not require a two-phase commit
* `numeric_static_map` allows the definition of a static mapping between input column values and keyspace IDs via a JSON file
