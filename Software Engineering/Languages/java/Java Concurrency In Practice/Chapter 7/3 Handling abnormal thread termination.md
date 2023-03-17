# 7.3 Handling abnormal thread termination

* One of the few times when you might want to consider catching `RuntimeException` is when you are calling unknown, untrusted code through an abstraction such as `Runnable`

## Example

* If a worker thread task in a thread pool throws an unchecked exception, let the thread die, but send notification that the thread has died so that some recovery may occur

```java
public void run() {
  Throwable thrown = null;
  try {
    while (!isInterrupted()) {
      runTask(getTaskFromQueue());
    }
  } catch (Throwable e) {
    thrown = e;
  } finally {
    exitThread(this, thrown);
  }
}
```

## 7.3.1 Uncaught exception handlers

* When a thread exits due to an uncaught exception, the JVM reports this event to the `UncaughtExceptionHandler`
* The handler could print an error message and a stack trace, or it could try restarting the thread, shutting down the application, send a notification, etc
* Specify the `UncaughtExceptionHandler` when constructing a `ThreadPoolExecutor`
* Exceptions thrown from tasks make it to the `UCEH` only if they are submitted with `execute`
  * Exceptions thrown (checked and unchecked) for tasks submitted with `submit` are considered part of the task's return status
  * These exceptions are rethrown by `Future.get` wrapped in an `ExecutionException`
