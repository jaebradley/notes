# 13.3.4 Blocking and Nonblocking I/O

* Blocking system call suspends the execution of an application
* Application moves from the operating system's run queue to a wait queue
* After the system call completes, the application is moved back to the run queue
* I/O device actions are generally asynchronous
* A non-blocking `read` returns immediately with whatever data is available
* An asynchronous `read` will complete the request in its entirety but will be completed at some time in the future
* The `select` system call takes a maximum time waiting time argument
  * if set to `0`, an application can poll for network activity without blocking
