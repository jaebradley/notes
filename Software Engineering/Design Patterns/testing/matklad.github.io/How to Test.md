# [How to Test](https://matklad.github.io/2021/05/31/how-to-test.html)

## Peeking Inside
* Checking for behavior that is not part of the output (i.e. a side-effect)
  * Example is a cache hit that is part of some execution logic
* Make this extra data a part of the system’s output by adding extra observability points
* Cargo tests look at the test’s output and the state of the resulting file system
  * Cargo also add verbose logging and prints information about cache hits and misses
  * This logging is used in assertions
