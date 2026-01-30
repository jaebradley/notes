# 8.4 Extending `ThreadPoolExecutor`

* `ThreadPoolExecutor` has several "hooks" (`beforeExecute`, `afterExecute`, and `terminated`)
* `afterExecute` is called whether the task completes by returning normally or by throwing an `Exception`
  * If the task completes with an `Error`, `afterExecute` is not called
* If `beforeExecute` throws a `RuntimeException`, the task is not executed and `afterExecute` is not called
* `terminated` hooks is called when the thread pool completes the shutdown process
  * After all tasks have finished and all worker threads have shut down

```java
protected void beforeExecute(Thread t, Runnable r) {
  super.beforeExecute(t, r);
  logger.fine("log the thread and the start time");
  startTime.set(System.nanoTime());
}

protected void afterExecute(Runnable r, Throwable t) {
 try {
   long endTime = System.nanoTime();
   // log task time
   // log number of tasks
 } finally {
   super.afterExecute();
 }
}

protected void terminated() {
  try {
    // log thread pool termination and average time per task
  } finally {
    super.terminated();
  }
}
```
