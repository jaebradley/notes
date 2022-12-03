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
