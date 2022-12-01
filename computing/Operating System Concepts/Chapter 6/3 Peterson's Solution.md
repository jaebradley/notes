# 6.3 Peterson's Solution

* Problem is two processes (P0 and P1) that are alternating between critical sections and remainder sections
* Processes share two data items - an integer `turn` value and a two-value boolean array `flag`
  * `turn` indicates which process is allowed to enter its critical section (`0` or `1`)
  * The `flag` array s used to indicate if a process is ready to enter its critical section
    * If `flag[i]` is `true` then Pi is ready to enter its critical section
