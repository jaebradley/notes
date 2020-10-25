# [Java: Multithreading - Part 1](https://blog.usejournal.com/java-multithreading-part-1-ec0c42bbead6)

* As the restaurant now has introduced more waiters (Threads) it is now utilizing the kitchen (CPU) staff (Cores) efficiently
  * It is serving more guests (Users) at any given time
* When a Java application starts up, the main thread (main method) is spawned by the Java process - this is the application entry point
  * From this point forwards, the entire applicaiton logic is executed in the main thread, or the threads that are spawned from within the application
* Heap is the shared memory area among threads where all objects live
* Stack is the private memory area allocated to each of the running threads
* Heap memory is garbage-collected to free up speace to remove objects that are no longer used / referenced while the stack is freed up once the thread of execution completes
* Every object has an intrinsic lock called a monitor - this is baked into the Java language by adding the keyword `synchronized` to the method signature (`public synchronized void open()`)
* Until a thread executing a synchronized method has completed execution, no other thread can execute the synchronized method or _any_ other synchronized method on the object
