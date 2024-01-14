# [Understanding the Cassandra Data Model from a SQL Perspective](https://www.rubyscale.com/post/143067472270/understanding-the-cassandra-data-model-from-a-sql)

## Mapping a Column Family to SQL Tables

* Column Family has any number of rows
* Each row has N column names and values
* Example is a "Users" column family
* Row key is a user UUID
* Column key/value pairs are username, password, email address, etc
* One way of implementing this in SQL is a table per user, with a `column name` column and a `column value` column
  * `column name` values would be unique
* Then, the values would be things like
  * `('username', 'joe')`
  * `('password', 'some password')`
  * `('email', 'joe@example.com')`
* In Cassandra, you can get a slice of keys, and then a slice of columns within those keys
* To find a user with a particular email address, need to make another column family that keys on email address and points to the user's UUID
  * In SQL, you can create an index to help optimize things you want to query
  * In Cassandra, have to manually maintain the index

## Mapping a Super Column Family to SQL tables

* Like a column family, but get a super column family name
* Guarantees uniqueness between a super column name and a column name
  * So a super column name of `bob` has a `street` and `city` column
  * A super column name `alice` has a `street` and `city` column
* Accessing a single column in a column family is fast and efficient
* Accessing a single column in a super column requires deserialization of the entire super column
* In the `alice` example, to get the `alice:street` value, Cassandra has to load everything in the `alice` super column
  * For a column family with 1000s of columns and a read-heavy load,performance could become impacted because all of the 1000s of columns would need to be deserialized just to get the value for one column
