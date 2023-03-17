# 6.3 Peterson's Solution

* Problem is two processes (P0 and P1) that are alternating between critical sections and remainder sections
* Processes share two data items - an integer `turn` value and a two-value boolean array `flag`
  * `turn` indicates which process is allowed to enter its critical section (`0` or `1`)
  * The `flag` array s used to indicate if a process is ready to enter its critical section
    * If `flag[i]` is `true` then Pi is ready to enter its critical section

```java
// Structure of current process in Peterson's solution
do {
  flag[current process number] = true;
  
  // If other process wants to enter the critical section, it can do so
  // If both processes try to enter at the same time, turn will be set to both i and j at roughly the same time
  // Only one of the assignments will be last - the other will be overwritten
  // The eventual value of turn will determine which of the two processes is allowed to enter its critical section first
  turn = other process number;
  while (flag[other process number] && turn == other process number) {
    // critical section
    
    flag[current process number] = false;
    
    // remainder section
  }
} while (true)
```

