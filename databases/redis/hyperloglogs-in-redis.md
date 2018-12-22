# [HyperLogLogs In Redis](https://robots.thoughtbot.com/hyperloglogs-in-redis)

* HyperLogLog is data structure used to count unique values (known as calculating cardinality of a set)
* Counting unique values with *exact* precision requires an amount of memory proportional to the number of unique values
  * This is because you need to compare next value to set of previously seen values
* For large data sets, memory is a limited resource so strategies, like using HyperLogLog, trade memory for precision
  * HyperLogLog estimates cardinalities larger than 10^9 with standard error of 2% using 1.5kb of memory

## In Redis

* Redis HyperLogLog uses 12kb of memory
  * Does not include bytes required to store keys
* Produces approximations with a standard error of 0.81%
* `PFADD` - adds members
  * Returns `1` if approximated cardinality has changed - `0` if not
* `PFCOUNT` - returns cardinality
  * Caches the calculated value in the last `8` bytes of the HyperLogLog
  * Invalidates this value on each `PFADD`
* `PFMERGE` - used to produce a HyperLogLog that approximates the cardinality of the union of two HyperLogLogs
  * `PFMERGE everyone visitors customers`
  * Can also replicate effect by doing an on-the-fly merge like `PFCOUNT visitors customers`
* HyperLogLogs are stored as strings and can be retrieved using `GET` and set using `SET`
  * `GET visitors => "HYLL\x01\x00\x00\x00\x03\x00\x00\x00\x00\x00\x00\x00E<\x94X\x10\x84Qi\x8cQD"`

## Theory

* Take case of person flipping coin
  * Given max number of heads flipped in a row, could you make any inferences about the number of total flips?
  * Generally, the higher the max number of heads flipped in a row, the higher likelihood that the total number of flips is also higher
* Applying this concept to the binary representation of numbers where a `0` is indicative of a heads (let's say)
  * Can estimate cardinality of a set of **uniformly distributed random numbers** by using the max number of leading `0`s in the binary representation of these numbers
  * If the maximum number of leading zeros is `n`, the estimated number of unique values in the set is `2^n`
  * So the analogy (in my mind) is that for the number of leading zeros (aka "heads in a row") to be high, a lot of numbers would have to be processed
* To ensure uniform distribution, use a hash function that converts to integers
* First few bits of hashed values are used to partition values
  * Each partition stores max number of leading zeros for it's subset
