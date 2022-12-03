# 6.5 Semaphores

* Consists of an integer variable that can only be accessed by two _atomic_ operations (`wait` and `signal`)
  * `wait` decrements the integer value
    * Traditional semaphores don't increment when the integer value is <= 0 (i.e. they spin lock until enough signals have occurred where the integer value is > 0)
  * `signal` increments the integer value
* Semaphores can be used to synchronize proocesses
  * There are two processes P1, and P2 that both share a common semaphore `synch`
  * Want to synchronize that P1 executes statement S1 before P2 executes statement S2

```
// synch is initialized with value 0

// P1
S1;
signal(synch);

// P2
// This wait will wait until P1's signal because synch has a value of 0
wait(synch);
S2;
```

## 6.5.2 Implementation

```
wait(S) {
  while S <= 0;
    // no-op
   S--;
}
```

* In this semaphore definition, ther eis a lot of busy waiting which wastes CPU cycles that could be used productively
 * While one process is in its critical section, all the other proceses that share the semaphore are blocked from entering their critical section / looping continuously
 * These spinlocks do not require a context switch (which can be expensive), so are useful when the locks are expected to be held for a short time
* Can modify semaphore definition such that a semaphore doesn't continue to spin / wait busily, but can instead block itself
 * Block operation places a process into a waiting queue associated with the semaphore, and the state of the process is switched to the waiting state
 * When a semaphore's value is > 0, the first process is pulled from the waiting queue and is moved from a waiting state to a ready state
 * Block and resume operations are provided by the operating system as basic system calls
 
```
typedef struct {
  int value;
  // can be a list of Process Control Blocks
  struct process *list;
} semaphore

wait(semaphore *S) {
  S->value--;
  if (S->value < 0) {
    add process to S->list;
    block(); // system call
  }
}

signal(semaphore *S) {
  S->value++;
  if (S->value <= 0) {
    remove next process from S->list;
    wakeup(next process);
  }
}
```

* `wait` and `signal` must be atomic
 * In a single-processor environment (i.e. 1 CPU), can solve this by inhibiting interrupts during the time `wait()` and `signal()` operations execute
  * This works because once interrupts are inhibited, instructions from different processes cannot be interleaved
 * Multiprocessor systems must use spinlocks to ensure that `wait` and `signal` are atomic (disabling interrupts on all processors is difficult and can diminish performance)
  * Busy waiting now moved from application program entry sections to critical sections of `wait` and `signal` operations
  * These sections should be short, and rarely occupied
