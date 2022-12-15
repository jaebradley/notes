# 8.1 Implicit couplings between tasks and execution policies

## 8.1.1 Thread starvation deadlock

* In a single-threaded executor, a task that submits another task to the same exectuor and waits for its result will always deadlock
  * Dependent task sits in the queue waiting for the initial task to finish, but the initial task won't finish as it is waiting for the result of the dependent task
* Can happen in larger thread pools if all threads are executing tasks that are blocked waiting for other tasks still on the work queue
* If your application uses a JDBC connection pool with 10 connections and each task needs a database connection, it is as if our thread pool only has 10 threads because any additional tasks will block waiting for a connection

## 8.1.2 Long-running tasks

* If the thread pool size is too small relative to the expected steady-state number of long-running tasks, eventually all the pool threads will run long-running tasks
* To mitigate long-running tasks, use timed resource waits instead of unbounded waits
  * This guarantees that each task eventually makes progress (either successful execution or failed completion)
  * Frees up threads for tasks that might complete more queickly
* If a thread pool is frequently full of blocked tasks, this may also be a sign that the pool is too small
