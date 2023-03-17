# 7.1 Task Cancellation

* Java does not provide any mechanism for safely forcing a thread to stop what it is doing
  * It provides interruption, a cooperative mechanism that lets one thread ask another to stop what it is doing
  * Rarely want a task, thread, or service to stop immediately, since it could leave shared data structures in an inconsistent state
* One potential cooperative mechanism for stopping a thread is by setting a cancellation requested flag that the task checks periodically

```java
# PrimeGenerator
private volatile boolean canclled;

public void run() {
  BigInteger p = BigInteger.ONE;
  while (!cancelled) {
    p = p.nextProbablePrime();
    synchronized (this) {
      primes.add(p);
    }
  }
}


public void cancel() {
  cancelled = true;
}
```

* Generator won't necessarily stop after exactly one second, since there may be some delay between the time that cancellation is requested and the time that the `run` loop next checks for cancellation
  * `cancel` is called from the `finally` block to ensure that the prime generator is cancelled even if the call to `sleep` is interrupted

```java
PrimeGenerator generator = new PrimeGenerator();
new Thread(generator).start();
try {
  SECONDS.sleep(1);
} finally {
  generator.cancel();
}
return generator.get();
```

* Tasks that want to be cancelled, must have a cancellation policy that specifies how other code can request cancellation, when the task checks whether cancellation has been requested, and what actions the task takes in response to a cancellation request
* `PrimeGenerator` has a simple cancellation policy: client code requests cancellation by calling `cancel`, `PrimeGenerator` checks for cancellation once per prime found, and exits when it detects cancellation has been requested

## 7.1.1 Interruption

* If a blocking method is called in `PrimeGenerator`, the same approach may never check the cancellation flag and never terminate
* Imagine a scenario where primes are generated and put on a `BlockingQueue`
  * If the producer gets ahead of the consumer, the queue fills up and `put`ting any more items on the queue will block
  * If a consumer tries to cancel the producer while it is blocked (i.e. calls the `cancel` method), the `cancelled` boolean will be `true`, but since the producer is blocked inside the loop, it will never check the `cancelled` flag

```java
private final BlockingQueue<BigInteger> queue;
private volatile booelean cancelled = false;

public void run() {
  try {
    BigInteger p = BigInteger.ONE;
    while (!cancelled) {
      // This could block and prevent the Task from being cancelled
      queue.put(p = p.nextProbablePrime());
    } catch (InterruptedException consumed) { }
}
```


* Blocking library methods suport interruption, which is a cooperative mechanism for a thread to signal to another thread that it should, at its convenience, and if it feels like it, stop what it is doing and do something else
* Each thread has a boolean `interrupted` status
  * Interrupting a thread sets its interrupted status to `true`
  * The `Thread` class has methods for interrupting a thread (the `interrupt` method), `isInterrupted` returns the itnerrupted status of the target thread, the `interrupted` method clears the interrupted status of the current thread and returns its previous value - the only way to clear the interrupted status
* Blocking methods like `Thread.sleep` and `Object.wait` try to detect when a thread has been interrupted and return early
  * They respond to interruption by clearing the interrupted status and throwing `InterruptedException` indicating that the blocking operation completed early due to interruption
* If a thread is interrupted when it is not blocked, its interrupted status is set, and it is up to the activity being cancelled to poll the interrupted status to detect interruption
* Interruption does not actually interrupt a running thread, but requests that the thread interrupt itself at the next convenient opportunity
* If you call `interrupted` and it returns `true`, unless you're planning on swallowing the interruption, you should do someting with it - either throw `InterruptedException` or restore the interrupted status by calling `interrupt` again
* Interruption is usually the most sensible way to implement cancellation
* The previous prime producer blocking example can be fixed by using interruption instead of a boolean flag to request cancellation
  * There are two points where interruption may be detected - the blocking `put` and the polling before entering the block with the `put`
  * The `while (!cancelled)` is now `while (!Thread.currentThread().isInterrupted())`

## 7.1.2 Interruption policies

* Interruption policy determines how a thread interprets an interruption request - what it does, when one is detected
* Most sensible interruption policy is thread-level or service-level cancellation
  * Exit as quickly as practical, cleaning up if necessary, potentially letting some owning entity know that the thread is exiting
* A single interrupt request may have more than one desired recipient
  * Interrupting a worker thread in a thread pool can mean both "cancel the current task" and "shut down the worker thread"
* Tasks do not execute in threads they own - they borrow threads owned by a service such as a thread pool
* Code that doesn't own the thread (for a thread pool, anything outside the thread pool implementation), should be careful to preserve the interrupted status so that the owning code can eventually act on it, even if the "guest" code acts on the interruption as well
* Most blocking library methods throw `InterruptedException` in response to an interrupt since they will never execute in a thread they own
  * The most reasonable cancellation policy for task or library code is to get out of the way as quickly as possible and communicate the interruption back to the caller so that code higher up on the call stack can take further action
* A task doesn't need to drop everything when it detects an interruption - it can finish the task it is performing and then throw `InterruptedException`
* Whether a task interprets interruption as cancellation or takes some other action on interruption, it should take care to preserve the executing thread's interruption status
  * Ensure that the interruption status is restored via `Thread.currentThread().interrupt()`
* Because each thread has its own interruption policy, you should not interrupt a thrad unless you know what interruption means to that thread

## 7.1.3 Responding to interruption

* When calling an interruptible blocking method (`Thread.sleep`), there are two practical strategies for handling `InterruptedException`
  * Propagate the exception (there may be some task-specific cleanup), making your method an interruptible blocking method too
  * Restore the interruption status so that code higher up on the call stack can deal with it
* If task is a `Runnable` and cannot propagate `InterruptedException`, standard way of preserving interruption request is calling `interrupt` again
* Most code does not know what thread it will run in and so should preserve the interrupted status
* Activities that do not support cancellation but still call interruptible blocking methods will have to call them in a loop, retrying when interruption is detected
  * Save interruption status locally, and restore it just before returning
  * Setting the interrupted status too early could result in an infinite loop because most interruptible blocking methods check the interrupted status on entry and throw `InterruptedException` immediately if it is set

```java
// Noncancelable task that restores interruption before exit
boolean interrupted = false;
try {
  while (true) {
    try {
      // blocking method
      return queue.take();
    } catch (InterruptedException) {
      interrupted = true;
      // fall through and retry
    }
  }
} finally {
  if (interrupted) {
    Thread.currentThread().interrupt();
  }
}
```

## 7.1.4 Example: timed run

* Consider starting a `PrimeGenerator` and then interrupts it after a second
  * While `PrimeGenerator` may make longer than a second to stop, it will eventually noteice the interrupt and stop
  * Want to know if the task threw an exception - if `PrimeGenerator` throws an unchecked exception before the timeout expires, it will probably go unnoticed, since the `PrimeGenerator` runs in a separate thread that does not explicitly handle exceptions
* Example attempts to run an arbitrary `Runnable` for some amount of time
  * It runs the task in the current (calling) thread, and schedules a cancellation task to interrupt the thread after the specified time interval
  * Any unchecked exceptions thrown by the task will be caught by the caller of this method

```java
public static void timedRun(Runnable r, long timeout, TimeUnit unit) {
  final Thread taskThread = Thread.currentThread();
  cancelExec.schedule(new Runnable() {
    public void run() { taskThread.interrupt(); }
  }, timeout, unit);
  r.run();
}
```

* Since `timedRun` can be called from an arbitrary thread, it cannot know the calling thread's interruption policy
* If the task completes before the timeout, the cancellation task that interrupts the thread in which `timedRun` is called could go off after `timedRun` has returned to its caller
* If the underlying task is not responsive to interruption, `timedRun` will not return until the task finishes, which may be long after the desired timeout (if it finishes at all)

```java
public static void timedRun(final Runnable r, long timeout, TimeUnit unit) throws InterruptedException {
  class RethrowableTask implements Runnable {
    private volatile Throwable t;
    public void run() {
      try { r.run(); }
      catch (Throwable t) { this.t = t; }
    }
    void rethrow() {
      if (t != null) {
        throw launderThrowable(t);
      }
    }
  }

  RethrowableTask task = new RethrowableTask();
  final Thread taskThread = new Thread(task);
  taskThread.start();
  cancelExec.schedule(new Runnable() {
    public void run() { taskThread.interrupt(); }
  }, timeout, unit);
  taskThread.join(unit.toMillis(timeout));
  task.rethrow();
}
```

* The thread created to run thte task has its own execution policy
* Even if the task doesn't respond to interrupts, the timed run metod will return to its caller
* After starting the task thread, the task thread does a timed `join`
* After `join` returns, the task thread checks if an exception was thrown when executing the task, an dif so, rethrows it in the thread calling `timedRun`
* The saved `Throwable` is shared between the two threads, and is declared `volatile` to safely publish it from the task thread to the `timedRun` thread
* Because it uses `join`, we don't know if control was returned because the thread exited normally or because the `join` timed out

## 7.1.5 Cancellation via `Future`

* `ExecutorService.submit` returns a `Future` describing the submitted task
* `Future` has a `cancel` method that takes a boolean argument, returning a `boolean` indicating if the cancellation attempt was successful
  * Only indicates if able to deliver the interruption
  * When the value is `true` and the task is currently running in some thread, then that thread is interrupted
  * `Future.cancel(false)` means "don't run this task if it hasn't started yet", and should be used for tasks that are not designed to handle interruption
* The task execution threads created by the standard `Executor` implmentations implement an interruption policy that lets tasks be cancelled using interruption, so it is safe to use the `cancel` method when they are running in a standard `Executor`
  * Do not interrupt a thread pool directly when attempting to cancel a task, because you won't know what task is running when the interrupt request is delivered
  * Treat interruption as a cancellation request so that they can be cancelled through their `Future`s
* To implement the `timedRun` behavior using `Future`, submit a task to an `ExecutorService`, and get a `Future` representing the task
  * Retrieve the result of the task using the timed `Future.get`
  * If the computation throws an exception, it will be rethrown from the method
  * If a `TimeoutException` is thrown, it is ignored as the `finally` will cancel the task by calling `Future.cancel(true)`

```java
Future<?> task = taskExec.submit(runnable);
try {
  task.get(timeout, unit);
} catch (TimeoutException e) {
  // task will be cancelled in finally
} catch (ExecutionException e) {
  // exception thrown in task; rethrow
  throw launderThrowable(e.getCause());
} finally {
  // Harmless if task already completed
  // Will interrupt the underlying thread if running
  task.cancel(true);
}
```

## 7.1.6 Dealing with non-interuptible blocking

* If a thread is blocked performing synchronous socket I/O or waiting to acquire an intrinsic lock, interruption has no effect other than setting the thread's interruption status
* Can stop these threads by "interrupting" them by closing the socket they are reading off of, but requires more information about why they are blocked in the first place
* `read` and `write` methods in `InputStream` and `OutputStream` are not responsive to interruption, but closing the underlying socket makes any threads blocked in `read` or `write` throw a `SocketException`

