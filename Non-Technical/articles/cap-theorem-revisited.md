# [CAP Theorem: Revisited](http://robertgreiner.com/2014/08/cap-theorem-revisited/)

* In a distributed system (a collection of interconnected nodes that share data) you can only have two out of the three guarantees for a write / read pair
  * Consistency
  * Availability
  * Partition Tolerance

* Consistencey: A read is guaranteed to return the most recent write for a given client
* Availability: A non-failing node will return a reasonable response within a reasonable amount of time
* Partition Tolerance: The system will continue to function when network partitions occur

* Since networks are *always* unreliable, you must tolerate partitions in a distributed system
* This implies that the two options are `CP` and `AP`
  * `CP`
    * Wait for a a response from the partitioned node which could result in a timeout
    * System can also choose to return an error
    * For atomic reads and writes
  * `AP`
    * Return the most recent version of the data, which could be stale
    * The system state will also accept writes, which will be processed later
    * Use when there is flexibility around when data in the system schronizes
