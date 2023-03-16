# [How to Create a Virtual Thread](https://blog.rockthejvm.com/ultimate-guide-to-java-virtual-threads/)

* Virtual threads are an alternative implementation of the `java.lang.Thread` type
* Virtual threads store stack frames in the heap instead of the stack
  * Initial memory footprint of virtual threads are much smaller (few hundred bytes vs. megabytes)
  * Stack memory is fixed

## Example helper method

```java
private static Thread virtualThread(String name, Runnable runnable) {
  return Thread.ofVirtual()
    .name(name)
    .start(runnable);
}
```

## Example task

```java
static Thread bathTime() {
  return virtualThread(
    "Bath time",
    () -> {
      log("I'm going to take a bath");
      sleep(Duration.ofMillis(500L));
      log("I'm done with the bath");
    });
}
```

## Example usage with an `newThreadPerTaskExecutor` method

```java
@SneakyThrows
static void concurrentMorningRoutineUsingExecutorsWithName() {
  final ThreadFactory factory = Thread.ofVirtual().name("routine-", 0).factory();
  try (var executor = Executors.newThreadPerTaskExecutor(factory)) {
    var bathTime =
      executor.submit(
        () -> {
          log("I'm going to take a bath");
          sleep(Duration.ofMillis(500L));
          log("I'm done with the bath");
         });
    var boilingWater =
      executor.submit(
        () -> {
          log("I'm going to boil some water");
          sleep(Duration.ofSeconds(1L));
          log("I'm done with the water");
        });
    bathTime.get();
    boilingWater.get();
  }
}
```
