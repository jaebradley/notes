# [**How Java Virtual Threads Broke Netflix**](https://medium.com/javarevisited/how-java-virtual-threads-broke-netflix-5f7b491435cc)
* Traditional Java threads map one-to-one with operating system threads
  * They are expensive to create and manage which is why most applications do not create many threads and rely on thread pools instead
* Virtual threads are managed by the JVM
  * They are cheap to create - can have thousands / millions
  * They run on top of carrier threads, which are the real operating system threads
* JVM schedules virtual threads onto carrier threads
  * When a virtual thread executes it mounts onto a carrier thread
  * When the virtual thread is blocked on I/O, it unmounts
  * The carrier thread is now free to run another virtual thread
  * When the previously blocked operation completes, the virtual thread mounts again, potentially on a different carrier
* Continuation is the saved state of a virtual thread at a suspension point
  * Includes call stack and local variables
  * When the virtual thread is blocked, the JVM saves the continuation and unmounts
  * Later, when the virtual thread is no longer blocked, the JVM restores the continuation and resumes the thread
* Pinning occurs when a virtual thread cannot unmount from its carrier
  * In this case, the continuation is not saved and the carrier thread is stuck until the blocking operation finishes
  * Problematic as the blocked virtual thread ties up an entire operating system thread
  * Pinning occurs most commonly when blocking inside a synchronized block
  * JVM ties the monitor lock to the carrier thread preventing the virtual thread from unmounting

```java
static final Object monitor = new Object();

static void example2() throws InterruptedException {
    var latch = new CountDownLatch(1);

    Thread.ofVirtual()
            .name("vt-sleep-example")
            .start(() -> {
                synchronized (monitor) {
                    try {
                        // This blocks inside synchronized
                        Thread.sleep(2000); // pins carrier in Java 21
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                    }
                }
                latch.countDown();
            });

    latch.await();
}

static void example() throws InterruptedException {
    Map<String, String> cache = new ConcurrentHashMap<>();
    var latch = new CountDownLatch(1);

    Thread.ofVirtual()
            .name("vt-cache-example")
            .start(() -> {
                cache.compute("user:42", (key, oldValue) -> {
                    try {
                        // Simulate blocking DB call
                        Thread.sleep(1000); // pins carrier in Java 21
                    } catch (InterruptedException e) {
                        Thread.currentThread().interrupt();
                    }
                    return "fetched-from-db";
                });

                System.out.println("Value updated in cache");
                latch.countDown();
            });

    latch.await();
}
```

* Engineers saw carrier threads with little activity and assumed the system was idle when virtual threads were pinned to those carriers
* Before Java 24, monitors were tied to carrier threads
* From Java 24 onwards, monitors are tied to virtual threads
  * Virtual threads can acquire, hold, and release monitors independently of the carrier
