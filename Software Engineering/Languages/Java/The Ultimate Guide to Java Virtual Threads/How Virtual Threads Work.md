# [How Virtual Threads Work](https://blog.rockthejvm.com/ultimate-guide-to-java-virtual-threads/)

* JVM maintains a pool of platform threads (mapped one-to-one with an operating system thread), created and maintained by a dedicated `ForkJoinPool`
  * Equal to number of CPU cores
  * Cannot be greater than `256`
* JVM schedules each virtual thread's execution on a platform thread
  * Copies the virtual thread's "stack" from the heap to the stack of the platform / operating system thread
  * Platform thread then becomes the "carrier" thread for the virtual thread

## Example log message

```bash
08:44:35.390 [routine-1] INFO in.rcard.virtual.threads.App - VirtualThread[#23,routine-1]/runnable@ForkJoinPool-1-worker-2 | I'm going to boil some water
```

* `VirtualThread[#23,routine-1]` - identifies the virtual thread being executed
  * `23` is the thread identifier
  * `routine-1` is the thread name
* `ForkJoinPool-1-worker-2` - identifies the platform thread and the `ForkJoinPool` that is utilized
  * The default `ForkJoinPool` is `ForkJoinPool-1`
  * `worker-2` is the platform thread named `worker-2` of the default `ForkJoinPool`


## Blocking

* The first time the virtual thread blocks on a blocking operation, the carrier thread is released
  * Virtual thread "stack" is copied back to the heap
* Carrier thread can now execute other eligible virtual threads
* Some time in the future, blocking operation finishes, and the scheduler schedules the virtual thread for execution
* Retrieve the number of cores on a Mac using the following command: `sysctl hw.physicalcpu hw.logicalcpu`
