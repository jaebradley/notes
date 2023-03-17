# [Why Virtual Threads?](https://blog.rockthejvm.com/ultimate-guide-to-java-virtual-threads/)

* `java.lang.Thread` is an abstraction of an OS thread
* OS threads are expensive to create
  * Large amount of memory in the stack to store the thread context, native, and Java call stacks
  * Whenever the scheduler preempts a thread from execution, memory must be moved around
* The straightforward way to write concurrent programs is to create a new thread for every concurrent task
