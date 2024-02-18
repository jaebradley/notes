# [Pinned Virtual Threads](https://blog.rockthejvm.com/ultimate-guide-to-java-virtual-threads/#6-pinned-virtual-threads)

* JVM mounts virtual thread to a platform thread (acts as a carrier thread)
    * Thread executes until a blocking operation
    * Virtual thread is unmounted from the carrier thread
    * Scheduler decides which virtual thread to schedule on the carrier thread
* Two cases where a blocked virtual thread is not unmounted from the carrier thread
    * Executes inside a `synchronized` block
    * Calls a native method or a foreign function (like a call to a native library using JNI)

```java
static class Bathroom {
  synchronized void useTheToilet() {
    log("I'm going to use the toilet");
    sleep(Duration.ofSeconds(1L));
    log("I'm done with the toilet");
  }
}

static Bathroom bathroom = new Bathroom();

static Thread goToTheToilet() {
  return virtualThread(
      "Go to the toilet",
      () -> bathroom.useTheToilet());
}

@SneakyThrows
static void twoEmployeesInTheOffice() {
  var riccardo = goToTheToilet();
  var daniel = takeABreak();
  riccardo.join();
  daniel.join();
}
```

* For a thread pool with one thread, `riccardo` goes to the bathroom first, then `daniel` will go to the bathroom
* Increasing the `maxPoolSize` configuration from `1` to `2` will allow the JVM to add a new carrier thread to the pool when needed
    * In the above example, JVM adds a new thread to the pool when no free carrier threads are identified
    * Unlike in the one thread case, the `daniel` virtual thread is now scheduled on a new carrier thread
    * The `daniel` virtual thread executes concurrently
* `ReentrantLock`s don't "pin" virtual threads
