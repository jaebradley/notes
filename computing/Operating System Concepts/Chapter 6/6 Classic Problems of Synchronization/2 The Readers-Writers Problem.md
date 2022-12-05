# 6.6.2 The Readers-Writers Problem

* Scenario is a database with several concurrent processes (some may want to read database, some may want to write to the database)
* Writers have exclusive access to the shared database while writing to the database
* First readers-writers problem requires that no reader be kept waiting unless a writer has already obtained permissioon to use the shared object
* Second readers-writers problem requires that once a writer is ready, that writer performs the write as soon as possible

## Solution for first readers-writers problem

* Three shared data structures
  * a `mutex` semaphore, a `writer` semaphore, an `int` counting the number of reader processes
  * The `mutex` and `writer` semaphores are initialized to `1`, the reader count variable is initialized to `0`
  * The `writer` semaphore acts as a mutual-exclusion semaphore for writers
  
```
do {
  // since writer is initialized to 1, only one writer at any given time
  wait(writer);
  
  // writing is performed
  
  // release writer (increment
  signal(writer);
} while (TRUE);
```
