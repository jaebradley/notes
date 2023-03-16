# 5.4 Blocking and interruptible methods

* Threads may block or pause for several reasons (waiting for I/O completion, waiting to acquire a lock, waiting to wake up from `Thread.sleep`, etc)
* When a thread blocks, it is usually placed in one of the blocked thread states (`BLOCKED, WAITING, TIMED_WAITING`)
* A blocked thread must wait for an event that is beyond its control before it can proceed
  * When that external event occurs, the thread is placed back in the `RUNNABLE` state and becomes eligible for scheduling
* When a method can throw `InterruptedException`, it is telling you that it is a blocking method and that if it is interrupted, it will make an effort to stop blocking early
* `Thread` provides the `interrupt` method for interrupting a thread and for querying whether a thread has been interrupted
* One thread cannot force another thread to stop what it is doing and do something else
  * When thread A interrupts thread B, A is merely requesting that B stop what it is doing when it gets to a convenient stopping point, if it feels like it
* For library code, there are basically two choices when a called method throws `InterruptedException`
  * Rethrow the exception
  * When the exception cannot be rethrown (inside a `Runnable`) the `InterruptedException` must be caught and the interrupted status must be restored by calling the `interrupt` method on the current thread so that code higher up the call stack can see that an interrupt was issued
* Should not catch the `InterruptedException` and do nothing

```java
public void run() {
  try {
    processTask(queue.take());
  } catch (InterruptedException e) {
    Thread.currentThread().interrupt();
  }
}
```
