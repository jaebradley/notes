# 5.5 Synchronizers

## Latches

* A latch is a synchronizer that can delay the progress of threads until it reaches its terminal state
* A latch acts as a gate - until the latch reaches the terminal state, the gate is closed and no thread can pass, and in the terminal state, the gate opens, allowing all threads to pass
* Latches can be used to ensure that certain activities do not proceed until other one-time activities complete like
  * Ensuring a computation does not proceed until the resources it needs have been initialized
  * Ensuring that a service does not start until other services which it depends on have started
* `CountDownLatch` allows one or more threads to wait for a set of events to occur
  * Counter, initialized to a positive integer` counts down the number of events to wait for
  * `await` methods wait for the counter to reach zero, which happens when all events have occurred
    * `await` will block until counter reaches zero, the waiting thread is interrupted, or the wait times out

```java
public class TestHarness {
  public long timeTasks(int nThreads, final Runnable task) throws InterruptedException {
    final CountDownLatch startGate = new CountDownLatch(1);
    final CountDownLatch endGate = new CountDownLatch(nThreads);

    for (int i = 0l i < nThreads; i++) {
      Thread t = new Thread() {
        public void run() {
          try {
            startGate.await();
            try {
              task.run();
            } finally {
              endGate.countDown();
            }
          } catch (InterruptedException ignored) {}
        }
      }

      t.start();
    }

    long start = System.nanoTime();
    startGate.countDown();
    endGate.await();
    long end = System.nanoTime();
    return end - start;
  }
```

* `TestHarness` creates a number of threads that can run a task concurrently
* The starting gate latch, initialized with a count of one, makes all the threads wait on it so that none of them start working until they are all ready to start
  * The starting gate is counted down, and this executes the underlying task
* The ending gate allows the master thread to wait efficiently until all of the worker threads have finished (since the worker threads count down)

## `FutureTask`

* `FutureTask` implements `Future` which describes an abstract result-bearing computation
* A computation represented by a `FutureTask` is implemented with a `Callable`, the result-bearing equivalent of `Runnable`
  * Is either waiting to run, running, or completed
  * Completion can be "normal" completion, cancellation, or an exception
  * Once completed, it stays in that state forever
* `Future.get` will return immeditately if the task is completed
  * If not completed, it will block until the task transitions to completed and will return the result or throw an exception 
* `FutureTask` conveys the result from the thread executing the computation to the thread(s) retrieving the result

```java
public class Preloader {
  private final FutureTask<ProductInfo> future = new FutureTask<ProductInfo>(new Callable<ProductInfo>() {
    public ProductInfo call() throws DataLoadException {
      return loadProductInfo();
    }
  }

  private final Thread thread = new Thread(future);

  public void start() {
    thread.start();
  }

  public ProductInfo get() throws DataLoadException, InterruptedException {
    try {
      return future.get();
    } catch (ExcecutionException e) {
      Throwable cause = e.getCause();
      if (cause instanceof DataLoadException) {
        throw (DataLoadException) cause;
      } else {
        throw launderThrowable(cause);
      }
    }
  }
}
```

* Tasks described by `Callable` can throw checked and unchecked exceptions
* Whatever is thrown is wrapped in an `ExecutionException` and rethrown from `Future.get`
* There are three categories of `ExecutionException`s - checked exceptions thrown by the `Callable`, `RuntimeException` or an `Error`
* `launderThrowable` checks if error is `Runtime` if so, it returns the exception
  * If it's an `Error`, throws it, else throws `IllegalStateException`

## Semaphores

* Counting semaphores are used to control the number of activities that can access a certain resource or perform a given action at the same time
* A semaphore manages a set of virtual permits
* Activities can acquire permits and release permits when they are done with them
* If no permit is available, the `acquire` method blocks until one is (or until interrupted or the operation times out)
* `release` returns a permit to the semaphore
* Binary semaphore (i.e. a semaphore with a count of 1) is used as a mutex with nonreentrant locking semantics
  * Whoever holds the sole permit holds the mutex
* Semaphores are useful for implementing resource pools such as database connection pools
  * Can use the semaphore to block if the pool is empty and unblock whn it becomes nonempty again by acquiring a permit before accessing a resource from the pool and releasing the permit after putting a resource back into the pool
* Can use semaphores to turn any collection into a blocking bounded collection
  * A bounded `HashSet` can be implemented by adding logic in the `add` operation to acquire a permit before adding an item to the underlying collection
  * If the `add` operation does not actually add anything, the permit is released immediately
  * A successful remove operation releases a permit allowing more elements to be added

## Barriers

* Barriers are similar to latches in that they block a group of threads until some event has occurred
* All the threads must come together at a barrier point at the same time in order to proceed
* Latches wait for events while barriers wait for other threads
* "Everyone meet at McDonald's at 6PM - once you get there, stay there until everyone shows up, and then we'll figure out what we're going to do next"
* Barriers are often used in simulations, where the work to calculate one step can be done in parallel but all the work associated with a given step must complete before advancing to the next step
  * For n-body particle simulations, each step pcalculates an update to the position of each particle based on the locations and other attributes of the other particles
  * Waiting on a barrier between each update ensures that all updates for step `k` have completed before moving on to the next step
* Another form of barrier is `Exchanger`, a two-party barrier in which the parties exchange data at the barrier point
  * When one thread fills a buffer with data and the other thread consumes the data from the buffer, these threads could use an `Exchanger` to meet and exchange a full buffer for an empty one
  * Simplest approach might be for the filling task to exchange when the buffer is full and the emptying task exchanges when the buffer is empty
    * This minimizes number of exchanges but can delay processing of some data if the arrival rate of new data is unpredictable
  * Another approach is that the thread that fills the buffer exchanges when the buffer is full, but also when the buffer is partially filled and a certain amount of time has elapsed
