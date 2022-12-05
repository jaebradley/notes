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
// Structure for writer process
do {
  // since writer is initialized to 1, only one writer at any given time
  wait(writer);
  
  // writing is performed
  
  // release writer semaphore, may becgin execution of waiting reader processes or waiting writer process
  // decision is made by scheduler
  signal(writer);
} while (TRUE);
```

```
/// Structure for reader process

do {
  // the mutex semaphore ensures that readcount is only updated by one process
  wait(mutex);
  readcount++;
  
  // If process is only reader (first or last), it will wait on any writer process
  if (1 == readcount) {
    // if writing process is in critical section (i.e. has a lock on the writer semaphore)
    // if there are n reader processes, then 1 reader process is waiting for the writer semaphore
    // n - 1 reader processes are waiting for the mutex semaphore
    wait(writer);
  }
  signal(mutex);
  
  // reading is performed
  
  wait(mutex);
  readcount--;
  if (0 == readcount) {
    signal(writer);
  }
  signal(mutex);
} while (TRUE);
```
