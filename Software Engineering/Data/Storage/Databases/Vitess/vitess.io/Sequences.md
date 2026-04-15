# [Sequences](https://vitess.io/docs/23.0/reference/features/vitess-sequences/)
* MySQL `auto_increment` and Vitess Sequences share
  * Values may be burned by rolled-back transactions and gaps in generated and stored values are possible
  * Value stored by the primary instance resulting from the original statement is sent in the replication stream so replicas will have the same value when re-playing the stream
  * No strict guaratees about ordering: two concurrent statements may have commit times in one order but their auto-incrementing IDs in the opposite order
    * The value for the ID is reserved when the statement is issued, not when the transaction is committed

## Vitess Sequences
* Each sequence has a backing MySQL table which **must** be in an unshareded keyspace
* Uses a single row in the table to describe which value the sequence should have next
* In-memory block allocation where the backing MySQL table is done only every X IDs
* Associate a column in the table with the sequence
* `auto_increment` supports inserting rows into a table with defined auto-incrementing column values as well as without defined auto-incrementing column
  * `auto_increment` will keep track of the highest value used and thus the “next” value for the column
  * Vitess sequences do not do this
  * Avoid mixing insert statements that specify an incrementing column values and insert statements that do not specify this column value
