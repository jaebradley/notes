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
