# 4.3 Thread Libraries

* Thread libraries are either libraries entirely in user space with no kernel support where invoking functions in the library result in a local function call in user space and not a system call
  * Alternatively, there are kernel-level libraries directly supported by the operating system
  * Invoking a function, typically results in a system call to the kernel
  
## 4.3.3 java Threads

* Creating a new `Thread` object does not specifically create the new thread, but the `start` method
  * Calling `start` allocates memory and initializes a new thread in the JVM
  * Calls the `run` method, making the thread eligible to be run by the JVM
* In example integer summation program, it has two threads
  * Parent thread, which starts execution in the `main` method
  * Computation thread, which is created when the `start` method is called on the created `Thread` object
* If two or more threads are to share data in a Java program, the sharing occurs by passing references to the shared object to the appropriate threads
