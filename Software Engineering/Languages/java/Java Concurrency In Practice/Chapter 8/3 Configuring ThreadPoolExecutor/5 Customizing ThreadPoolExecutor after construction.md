# 8.3.5 Customizing `ThreadPoolExecutor` after construction

* `Executors` include the `unconfigurableExecutorService` factory method
  * This method takes an existing `ExecutorService` and wraps it with one exposing an `ExecutorService` that exposes only the methods of `ExecutorService`
  * This prevents further configuration (like if the `Executor` is created through one of the factory methods in `Executors`)
* `newSingleThreadedExecutor` returns an `ExecutorService` that is similarly "unconfigurable"
  * This prevents some misguided code from trying to increase the pool size on a single-threaded executor, undermining the intended execution semantics
