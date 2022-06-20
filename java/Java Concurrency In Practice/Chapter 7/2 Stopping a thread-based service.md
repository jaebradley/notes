# 7.2 Stopping a thread-based service

* You should not manipulate a thread unless you own it
* While the `Thread` class does not have an explicit owner, the "owner" is generally thought of as the class that created the thread
  * A thread pool owns its worker threads and if those threads need to be interrupted, the thread pool should take care of it
* Application may own service, and service may own the worker threads, but that doesn't mean the application owns the worker threads
  * Service should provide lifecycle methods for shutting itself down that also shut down the owned threads
  * `ExecutorService` provides the `shutdown` and `shutdownNow` methods

## 7.2.1 Example: a logging service

* Logging is moved to a separate logging thread
* `LogWriter` hands the message off to a logging thread via a `BlockingQueue` and the logging thread writes it out
  * Multi-producer, single consumer design - any method calling the `log` method is the producer and the background logger thread is the consumer
  * If the logger thread falls behind, the `BlockingQueue` eventually blocks the producers until the logger thread catches up
* Simply implementing the logger thread to exit on interruption may discard log messages
 * This is problematic for threads blocked in the `log` method because when the `BlockingQueue` is full these threads will never become unblocked
* Cancelling producer-consumer activity requires cancelling the producers AND the consumers
* Set a "shutdown requested" flag to prevent further metssages from being submitted
* Wait for consumers to drain the queue and unblock any producers in `log`

```java
public void log(String msg) throws InterruptedException {
  if (!shutdownRequested) {
    queue.put(msg);
  } else {
    throw new IllegalStateException("logger is shut down");
  }
}
```

* `log` is check-then-act, which means that there is a race condition where some producers might see that the service has not been shut down, get blocked in `log` and never become unblocked
* Make submission of a new log message an atomic action
  * Don't want to hold a lock while attempting to enqueue the message, since `put` could block

```java
public class LoggingService {
  @GuardedBy("this") private boolean isShutdown;
  @GuardedBy("this") private int reservations;

  public void stop() {
    synchronized (this) {
      isShutdown = true;
    }
    loggerThread.interrupt();
  }

  public void log(String message) {
    synchronized (this) {
      if (isShutdown) {
        throw new IllegalStateException();
      }
      ++reservations;
    }
    queue.put(msg);
  }

  private class LoggerThraed extends Thread {
    public void run() {
      try {
        while (true) {
          try {
            synchronized (LoggingService.this) {
              if (isShutdown && reservations == 0) {
                break;
              }
            }

            String msg = queue.take();
            synchronized (LoggingService.this) {
              --reservations;
            }
            writer.println(msg);
          } catch (InterruptedException e) {
            // retry
          }
        }
    } finally {
      writer.close();
    }
  }
}
```

## 7.2.2 `ExecutorService` shutdown

* The `ExecutorService` offers two ways to shut down - graceful shutdown via the `shutdown` method and abrupt shutdown via the `shutdownNow` method
  * `shutdownNow` returns the list of tasks that had not yet started after attempting to cancell all actively executing tasks
  * Abrupt termination is faster but riskier because tasks may be interrupted in the middle of execution
  * Normal termination is slower but safer because the `ExecutorService` does not shut down until all queued tasks are processed
* More sophisticated services are likely to encapsulate an `ExecutorService` behind a higher-level service that provides its own lifecycle methods

```java
public class LogService {
  private final ExecutorService exec = new SingleThreadExecutor();

  public void stop() throws InterruptedException {
    try {
      exec.shutdown();
      exec.awaitTermination(TIMEOUT, UNIT);
    } finally {
      writer.close();
    }
  }

  public void log(String msg) {
    try {
      exec.execute(new WriteTask(msg));
    } catch (RejectedExecutionException ignore) { }
  }

}
```

## 7.2.3 Poison Pills

* Use a poison pill (a recognizable object) that's placed on the queue that means "when you get to this, stop"
* Producers should not submit any work after putting a poison pill on the queue

```java
public class CrawlerThread extends Thread {
  public void run() {
    try {
      crawl(root);
    } catch (InterruptedException e) { }
    finally {
      while (true) {
        try {
          queue.put(POISON);
          break;
        } catch (InterruptedException e1) { /* retry */ }
      }
    }
  }
}

public clas IndexerThread extends Thread {
  public void run() {
    try {
      while (true) {
        File file = queue.take();
        if (file == POISON) {
          break;
        } else {
          indexFile(file);
        }
      }
    } catch (InterruptedException consumed) { }
  }
}
```

* Poison pills only work when the number of producers and consumers is known
  * Approach can be extended to multiple producers by having each producer place a pill on the queue and having the consumer stop only when it receives N pills
  * Approach can be extended to multiple consumers by having each producer place a pill for every consumer on the queue
  * Can get large for large numbers of producers and consumers
* Poison pills only work for unbounded queues
