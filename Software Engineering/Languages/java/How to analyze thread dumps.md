# [How to analyze thread dumps](https://confluence.atlassian.com/confkb/how-to-analyze-thread-dumps-788039144.html)

* Thread dump is a snapshot of everything that an application is doing right at the instant the thread dump is taken
* If an application log is a diary of a vacation, a thread dump is a photo
* Thread dump is a collection of stack traces, one for each thread that's running on the instance
* The thread name (like `http-bio-80-exec-77`) contains information like
  * The port the instance is running on (`80`)
  * The connector that's being used (`http-bio`) which is the Java BIO connector, the default for Tomcat 7
  * The thread number (`77`)

## Long running threads

* Take a thread dump every 10 seconds should reveal any long-running threads
  * Long-running threads are threads that are stuck or are taking a very long time to complete, which is almost universally where a performance problem lies
  * Long-running threads will have the same stack trace between two dumps, implying that the thread has been working in the same method / executing the same logic between the two dumps

## Thread states

* `RUNNABLE` - the thread is actively doing something
* `BLOCKED` - the thread has something to do, but can't do it until a shared resource is free
  * `BLOCKED` threads can be of interest as it represents a bottleneck in the application
* `WAITING` and `TIMED_WAITING`
  * These threads were explicitly told to do nothing
  * `TIMED_WAITING` usually includes a timeout while a `WAITING` thread will wait indefinitely
