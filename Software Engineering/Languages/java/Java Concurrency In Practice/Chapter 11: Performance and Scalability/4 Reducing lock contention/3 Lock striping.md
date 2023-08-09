# 11.4.3 Lock striping

* Lock striping is partition-based locking on a variable-sized set of independent objects
* `ConcurrentHashMap` uses an array of 16 locks
  * Each lock guards a hash bucket
* Assuming a well distributed hash function
