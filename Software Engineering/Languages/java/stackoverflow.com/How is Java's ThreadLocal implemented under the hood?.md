# [How is Java's ThreadLocal implemented under the hood?](https://stackoverflow.com/a/15653015)
* A naive from-scratch implementation of `ThreadLocal` may use `ConcurrentHashMap<Thread, T>` with the `Thread.currentThread` value used as the hash map key
  * `ConcurrentHashMap` write access by different threads may lead to contention
  * Would need to release `Thread` pointers from the map after a Thread has finished and been garbage-collected
* Could get around the garbage-collection issue by using weak references
  * So ` Collections.synchronizedMap(new WeakHashMap<Thread, T>())` or `new MapMaker().weakKeys().makeMap()`
  * So once no one else is holding onto the Thread the key/value pair can be garbage collected
* Think about `ThreadLocal` as a mapping of objects to values in each Thread
  * So each Thread stores a mapping of values, and `ThreadLocal` provides an interface to introspect and access that mapping
  * So each `Thread` has a map (`ThreadLocal.ThreadLocalMap`) where this mapping is maintained by the `ThreadLocal` class
* `ThreadLocalMap` is not a `WeakHashMap` but follows a very similar contract, holding keys by weak reference
* Basically: use a map *in this Thread* to hold all `ThreadLocal` objects
  * Thus, there’s no reason to worry about values in other Threads as `ThreadLocal` can only access values in the current Thread and thus there are no concurrency issues
  * Once the Thread has completed, the underlying `ThreadLocal` mapping will be garbage-collected and all related objects will be cleaned up
  * Even if the `Thread` object is held onto, `ThreadLocal `objects are held on to by weak reference, and can be cleaned up as soon as the `ThreadLocal `object goes out of scope
* [JDK 8's `ThreadLocal` implementation](https://hg.openjdk.org/jdk8/jdk8/jdk/file/tip/src/share/classes/java/lang/ThreadLocal.java)
