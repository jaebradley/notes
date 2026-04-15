# [Custom Sharding With Vitess](https://vitess.io/blog/2017-09-18-custom-sharding-with-vitess/)
* Example is a database that is already sharded 4-ways
* Using an input column (`user_id`) the sharding function yields a value from 0 to 3, inclusive
* Assign keyranges to each of the shards
* Define a Vindex that maps the input, like a `user_id`, to a keyspace identifier that corresponds to a keyrange
* Keyspace ID can be any binary string
* In this example, keyspace IDs are big-endian representations of 64-bit numbers
* Fixed length of 8 bytes, and this keyspace can be split into 4 numerical ranges
* Need a function that maps user IDs into these key ranges
