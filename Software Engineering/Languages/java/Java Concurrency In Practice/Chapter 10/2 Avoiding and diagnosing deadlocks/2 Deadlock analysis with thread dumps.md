# 10.2.2 Deadlock analysis with thread dumps

* Thread dump contains a stack trace for each running thread
* Thread dump includes lock information (which lock is being held by which thread)
* Before generating thread dump, the JVM searches the is-waiting-for graph to identify cycles
* In the book's example, one thread has a lock on the `MummbleDBConnection` and is waiting to acquire the lock for the `MumbleDBCallableStatement`
  * Other thread has a lock on `MumbleDBCallableStatement` and is waiting to acquire the lock on `MummbleDBConnection`
  * Nothing in the JDBC specification that requires a `Connection` to be thread-safe
  * Common to confine use of a `Connection` to a single thread
  * Vendor tried to deliver a thread-safe JDBC driver, but did not take lock ordering into account
