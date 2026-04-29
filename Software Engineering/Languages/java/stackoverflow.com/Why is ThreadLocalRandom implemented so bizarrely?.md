# [Why is ThreadLocalRandom implemented so bizarrely?](https://stackoverflow.com/a/41760223)
* `Random` was designed to be “thread-safe” by synchronizing all its methods
* This works in that `Random` can be used across multiple threads, but since only one thread can retrieve random data, there is contention when accessing `Random`
* Constructing a `ThreadLocal<Random>` object can avoid this contention, but there is still some overhead with `synchronized` methods even when uncontested
  * Constructing `n` `Random` instances (one per thread) is wasteful
* Implementation of `ThreadLocalRandom` stores the seed in the related `Thread` class, which allows `ThreadLocalRandom` to be stateless
  * This allows a single instance of `ThreadLocalRandom` to exist across the whole application
* [JDK 8 `ThreadLocalRandom` implementation](https://hg.openjdk.org/jdk8/jdk8/jdk/file/687fd7c7986d/src/share/classes/java/util/concurrent/ThreadLocalRandom.java#l123)