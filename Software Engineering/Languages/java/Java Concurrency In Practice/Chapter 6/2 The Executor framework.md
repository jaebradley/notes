# 6.2 The Executor framework

* The `Executor` interface is based on the producer-consumer pattern, where activities that submit tasks are the producers (producing units of work to be done)
  * The threads that execute tasks are the consumers (consuming the produced units of work)

## Example: web server using Executor

```java
private static final Executor exec = Executors.newFixedThreadPool(100);

public static void main(String[] args) throws IOException {
  ServerSocket socket = new ServerSocket(80);
  // similar request handling logic as in 6.1
  while (true) {
    // make connection
    // create Runnable task
    exec.execute(task);
  }
}
```

## Execution policies

* Execution policy is the what, where, when, and how, of task execution including
  * What thread will execute the task
  * What order should tasks be executed
  * How many tasks may execute concurrently
  * How many tasks are queued pending execution
  * If a task has to be rejected, which task should be selected as the victim
* Whenever a new thread is created like `new Thread(runnable).start()`, consider replacing with the use of an executor

## Thread pools

* A thread pool manages a homogeneous pool of worker threads
* A thread pool is tightly bound to a work queue holding tasks waiting to be executed
* Worker threads request the next task from the work queue, execute it, and go back to waiting for another task
* Reusing an existing thread instead of creating a new one amortizes thread creation and teardown costs over multiple requests
  * Any latency associated with task creation does not delay task execution
  * Can tune the size of the thread pool, so that there are enough threads to keep the processor busy, while not having so many that your application runs out of memory
* `newFixedThreadPool` keeps a fixed-size thread pool, creating threads as tasks are submitted, up to the maximum pool size
* `newCachedThreadPool` will reap idle threads when the current pool size exceeds the demand for processing, or to add new threads when demand increases, but places no bounds on the size of the pool
* Submitting a task with `execute` adds the task to the work queue and the worker threads repeatedly dequeue tasks from the work queue and execute them

## Executor lifecycle

* The JVM can't exit until all the threads have terminated, so failing to shut down an Executor could prevent the JVM from exiting
* In shutting down an application, there is a spectrum from graceful shudown (finish anything that has been started, but don't accept any new work) and abrupt shtudown (turn off the power to the machine room)
* The `ExecutorService` interface extends the `Executor` interface, and adds methods for lifecycle management
* The `shutdown` method initiates a graceful shutdown where no new tasks are accepted but previously submitted tasks are allowed to complete, including those that have not yet begun execution
* `shutdownNow` initiates an abrupt shutdown - it attempts to cancel outstanding tasks and does not start any tasks that are queued but not begun
* Tasks submitted to an `ExecutorService` after it has been shut down are handled by the rejected execution handler, which might silently discard the task, or might cause `execute` to throw the unchecked `RejectedExecutionException`
* Once all tasks have completed, the `ExecutorService` transitions to the terminated state
* Can wait for the `ExecutorService` to reach the terminated state with `awaitTermination` or poll the `isTerminated` method
* It is common to follow `shutdown` immediately by `awaitTermination`

```java
class LifecycleWebServer {
  private final ExecutorService exec = ...

  public void start() throws IOException {
    ServerSocket socket = new ServerSocket(80);
    while (!exec.isShutdown()) {
      try {
        final Socket conn = socket.accept();
        exec.execute(new Runnable() {
          public void run() { handleRequest(conn); }
        });
      } catch (RejectedExecutionException e) {
        if (!exec.isShutdown()) {
          log("task submission rejected", e);
        }
      }
    }
  }

  public void stop() { exec.shutdown(); }
}
```
