# [Replication Formats - Statement vs Row-based](https://arpitbhayani.me/blogs/replication-formats)

## Statement-based Format
* Primary records operation as an event in the log file
* When the replica reads this log event, it executes the same operation on the replica's database
* So `UPDATE tableX SET column_y = 'some value' WHERE column_z = 'some identifier'` would be copied to the log file
* Advantages
  * These statements serve as an audit trail and are the verbatim operations that occurred on the primary
  * Keeps log file size compact
* Disadvantages
  * When the primary executes non-deterministic operations like generating a UUID or executing RAND or NOW, these will generate different values on the replica, leading to data corruption

## Row-based Format
* Primary node logs updates on the individual rows instead of the overall statement that was executed
* Log file entries reflect how row data changed in the primary database
* When the replica reads the log file, it updates its database by applying the changes on its rows
* If `UPDATE tableX SET column_y = 'some value' WHERE column_z = 'some identifier'` changed five rows, then these five rows would each have an entry in the log file like `tableX:rowID column_y='some value'` 
* Non-deterministic operations are safe as the written log file value is the result of the non-deterministic computation
* Primary node locks the replication log file when completing an operation
  * There can be many events to record in the log file, and thus this lock can have a long duration, negatively impacting replication throughput
  * There can also be fan out and the logfile can take significant storage space
