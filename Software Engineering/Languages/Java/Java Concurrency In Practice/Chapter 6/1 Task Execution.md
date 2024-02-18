# Task Execution

## Executing tasks in threads

* Tasks are independent - they are work that doesn't depend on the state, result, or side effects of other tasks
* Most server applications offer a natural choice of task boundary: individual client requests
  * Submitting a message to a mail server is not affected by the other messages being processed at the same time
  * Handling a single message usually requires a very small percentage of the server's total capacity
* Processing a web request involves a mix of computation and I/O
  * The server must perform socket I/O to read the request and write the response, which can block due to network congestion or connectivity problems
  * If one request blocks, this also prevents pending requests from being processed at all
  * Resource utilization is poor, since the CPU sits idle while the single thread waits for its I/O to complete

## Explicitly creating threads for tasks

* Can create a new thread for each task like

```java
ServerSocket socket = new ServerSocket(80);
while (true) {
  final Socket connection = socket.accept();
  Runnable task = new Runnable() {
    public void run() {
      handleRequest(connection);
    }
  }

  new Thread(task).start();
}
```

* For each connection, the main thread creates a new thread to process the request, instead of processing it within the main thread
* Main loop can resume waiting for the next incoming connections more quickly, enabling new connections to be accepted before previous requests complete, improving responsiveness
* Tasks can be processed in parallel, enabling multiple requests to be serviced simultaneously, improving throughput if there are multiple processors or if tasks block due to I/O, for example
* Task-handling code must be thread-safe because it may be invoked concurrently for multiple tasks

## Disadvantages of unbounded thread creation

* If requests are frequent, creating a new thread for each request can consume significant computing resources
* When there are more runnable threads than available processors, threads sit idle
  * Having many idle threads can tie up a lot of memory, putting pressure on the garbage collector
* There is a limit on the number of threads that can be created
  * This limit can vary by platform and operating system
  * When the limit is met, the most likely result is an `OutOfMemoryError`
