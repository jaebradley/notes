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

## 7.2.5 Limitations of `shutdownNow`

* When an `ExecutorService` is shut down abruptly with `shutdownNow`, it attempts to cancel the tasks currently in progress and returns a list of tasks that were submitted but never started so that they can be logged or saved for later processing
* There is no way of knowing the state of the tasks in progress at shutdown time unless the tasks themselves perform some soft of checkpointing
  * To know which tasks have not completed, you need to know not only which tasks didn't start, but also which tasks were in progress when the executor was shut down
* Through `execute` method instrumentation, can remember which tasks were cancelled after shutdown, and can identify which tasks started but did not complete normally

```java
public class TrackingExecutor extends AbstractExecutorService {
  private final ExecutorService exec;
  private final Set<Runnable> tasksCancelledAtShutdown = Collections.synchronizedSet(new HashSet<Runnable>());

  public List<Runnable> getCancelledTasks() {
    if (!exec.isTerminated()) {
      throw new IllegalStateException();
    }

    return new ArrayList<Runnable>(tasksCancelledAtShutdown);
  }

  public void execute(final Runnable runnable) {
    exec.execute(new Runnable() {
      public void run() {
        try {
          runnable.run();
        } finally {
          if (isShutdown() && Thread.currentThread().isInterrupted()) {
            tasksCancelledAtShutdown.add(runnable);
          }
        }
      }
    });
  }
}
```

* Example of a `WebCrawler` that applies a `TrackingExecutor`
  * Web crawing is unbounded, so if a crawler is shut down, want to save its state so it can be restarted later
  * When the crawler is shut down, the tasks that did not start and those that were cancelled are scanned and their URLs are recorded, so that those URLs can be added to the queue when the crawler restarts
* Unavoidable race condition that yields tasks that are identified as cancelled but actually completed
  * This occurs when the thread pool is shut down between when the last instruction of the task executes and when the pool records the task as complete
  * If tasks are idempotent (if performing them twice has the same effect as performing them once)

```java
public absract class WebCrawler {
  private volatile TrackingExecutor exec;
  @GuardedBy("this")
  private final Set<URL>urlsToCrawl = new HashSet<URL>();

  public synchronized void start() {
    exec = new TrackingExecutor(Executors.newCachedThreadPool());
    for (URL url : urlsToCrawl) {
      submitCrawlTask(urL);
    }
    urlsToCrawl.clear();
  }

  public synchronized void stop() throws InterruptedException {
    try {
      saveUncrawled(exec.shutdownNow());
      // "Blocks until all tasks have completed execution after a shutdown request, or the timeout occurs, or the current thread is interrupted, whichever happens first."
      // https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ExecutorService.html
      if (exec.awaitTermination(TIMEOUT, UNIT)) {
        saveUncrawled(exec.getCancelledTasks());
      }
    } finally {
      exec = null;
    }
  }

  private void saveUncrawled(List<Runnable> uncrawled) {
    for (Runnable task: uncrawled) {
      urlsToCrawl.add((CrawlTask) task).getPage());
    }
  }

  private void submitCrawlTask(URL u) {
    exec.execute(new CrawlTask(u));
  }


  private class CrawlTask implements Runnable {
    private final URL url;

    public void run() {
      for (URL link : processPage(url)) {
        if (Thread.currentThread().isInterrupted()) {
          return;
        }

        submitCrawlTask(link);
      }
    }

    public URL getPage() {
      return url;
    }
  }
}
```
