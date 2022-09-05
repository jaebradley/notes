# 4.2 Multithreading Models

* User threads are threads provided at the user level
* Kernel threads are threads supported and managed directly by the operating system

## 4.2.1 Many-to-One Model

* Many user-level threads are mapped to one kernel thread
* Thread management is done by the thread library in user space, so it is efficient, but the entire process will block if a thread makes a blocking system call
* Only one thread can access the kernel at a time, so multiple threads are unable to run in parallel on multiprocessors

## 4.2.2 One-to-one Model

* Each user thread is mapped to a kernel thread
* Allows other threads to run if a thread makes a blocking system call
* Allows multiple threads to run in parallel on multiprocessors
* Overhead of creating kernel threads
* Most implementations restrict the number of threasd supported by the system
* Linux implements the one-to-one model

## 4.2.3 Many-to-Many Model

* Multiplexes many user-level threads to a smaller or equal number of kernel threads
* Developers can create as many user threads as necessary, and the corresponding kernel threads can run in parallel on a multiprocessor
* When a thread performs a blocking system call, the kernel can schedule another thread for execution
