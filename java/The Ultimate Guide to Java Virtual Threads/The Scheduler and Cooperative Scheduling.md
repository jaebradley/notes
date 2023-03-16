# [The Scheduler and Cooperative Scheduling](https://blog.rockthejvm.com/ultimate-guide-to-java-virtual-threads/#5-the-scheduler-and-cooperative-scheduling)

* Virtual threads implement cooperative scheduling
* Virtual threads decide when to yield execution to another virtual thread

## Example using infinitely executing (blocking) task

```java
static Thread workingHard() {
  return virtualThread(
      "Working hard",
      () -> {
        log("I'm working hard");
        while (alwaysTrue()) {
          // Do nothing
        }
        sleep(Duration.ofMillis(100L));
        log("I'm done with working hard");
      });
}

static Thread takeABreak() {
  return virtualThread(
      "Take a break",
      () -> {
        log("I'm going to take a break");
        sleep(Duration.ofSeconds(1L));
        log("I'm done with the break");
      });
}

@SneakyThrows
static void workingHardRoutine() {
  var workingHard = workingHard();
  var takeABreak = takeABreak();
  workingHard.join();
  takeABreak.join();
}
```

* If there is a pool of only a single carrier thread, then the `workingHard` virtual thread will never reach the "blocking" operation (the `sleep`)
  * Since it will never reacha blocking operation, it will never yield execution to the `takeABreak` virtual thread

```java
static Thread workingConsciousness() {
  return virtualThread(
      "Working consciousness,
      () -> {
        log("I'm working hard");
        while (alwaysTrue()) {
          sleep(Duration.ofMillis(100L));
        }
        log("I'm done with working hard");
      });
}
```

* Works, but stops / blocks every `100` ms
* Execution reaches blocking operation, and the `workingHard` virtual thread can be unmounted from the carrier thread
* If the configuration is changed with two carrier threads
  * `workingHard` will be stuck in an infinite loop on one carrier thread
  * `takeABreak` will execute on the other carrier thread
* Since virtual threads release carrier threads only on blocking operations, cooperative scheduling and virtual threads will not improve the performance of CPU-intensive applications
