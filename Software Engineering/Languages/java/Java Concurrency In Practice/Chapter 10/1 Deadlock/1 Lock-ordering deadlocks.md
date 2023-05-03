# 10.1.1 Lock-ordering deadlocks

* Class with a "left" and "right" lock
  * The `leftRight` method attempts to acquire the left lock and then the right lock
  * The `rightLeft` method attempts to acquire the right lock and then the left lock
* Two threads can call `leftRight` and `rightLeft` in such a way that they end up deadlocking
  * Thread 1 calls `leftRight` and acquires the left lock
  * Thread 2 calls `rightLeft` and acquires the right lock (before Thread 1 acquires the right lock)
  * Thread 1 waits to acquire the right lock from Thread 2
  * Meanwhile Thread 2 is waiting to acquire the left lock from Thread 1
* If it's guaranteed that every thread that needs lock A and B at the same time always acquire A and B in the same order, no deadlock will exist
