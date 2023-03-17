# 7.4 JVM shutdown

* Orderly shutdown occurs when the last nondaemon thread terminates, `System.exit` is called, `SIGINT`
* Abrupt shutdown occurs when `Runtime.halt` is called, or by killing the JVM process through a `SIGKILL`

## 7.4.1 Shutdown hooks

* The shutdown hooks are unstarted threads that are registered with `Runtime.addShutdownHook`
* These shutdown hooks are run in an orderly shutdown
* The JVM makes no guarantees about the order of the shutdown hooks
* Application threads continue to run concurrently with the shutdown process
* When all of the shutdown hooks have completed, the JVM may run finalizers, then it halts
* JVM makes no attempt to stop or interrupt running application threads, instead, abruptly terminating the application threads
* Shutdown hooks should be thread-safe, and should attempt to minimize delaying JVM termination
* Shutdown hooks are generally used for application cleanup like removing temporary files
* A single shutdown hook for all services ensures that the shutdown actions are executed sequentially in a single thread
  * This may be useful for cleaning up resources, like a log file, that may be in use by other shutdown hooks

## 7.4.2 Daemon threads

* When the JVM starts up, the threads that it creates are daemon threads, like the garbage collector, except for the main thread
* When a new thread is created, it inherits the daemon status of the thread that crated it
* So threads that are created by the main thread are also "normal" threads
* When a normal thread exits, the JVM checks to see if the only running threads are daemon threads
  * If there are no normal threads remaining, the JVM initiates an orderly shutdown
  * There is no shutdown for the daemon threads, the JVM just exits
  * There are not many processing activities that can be safely abandoned with no cleanup, so daemon threads have rare use-cases (like removing expired entries from an in-memory cache)
