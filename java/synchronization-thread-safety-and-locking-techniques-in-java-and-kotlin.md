# [Synchronization, Thread-Safety And Locking Techniques In Java And Kotlin](https://proandroiddev.com/synchronization-and-thread-safety-techniques-in-java-and-kotlin-f63506370e6d)

* `volatile` fields provide memory visility and guarantee that the value that is being read comes from main memory and not the CPU's cache so the value in the CPU's cache is always considered dirty and has to be refetched
  * Does not help in the case where multiple threads are trying to update a counter because other threads can still read the same value while other threads try to increment it - the entire increment function needs to be atomic
* Synchronized resembles a lock on the door that has only one key, which people need to use to open the door and lock it
  * When a person (a thread) enters the room, they can use the key to lock the door, and since no one else has the key, they cannot go in until the key is returned to them by the original person
  * This also implies that access to the whole synchronized method is restricted, even when we don't have anything to do with synchronized objects
  * Instead of "locking" an entire method down, can also use synchronized statements, which take an object to lock, and then hold the lock for the length of the code block
  * In following example, `synchronized(this)` refers to an `Incrementor` instance as the lock object, which locks the instance, and does the work inside the code-block and then releases the lock

```java
synchronized(this) {
  // lock is acquired in this block
  this.doSomeWork();
}

// no longer locked
```

* `java.util.concurrent.atomic` also provides atomic primitives, like `AtomicInteger`, which provide mutating functions for the underlying primitive values that are atomic and thread-safe

```java
AtomicInteger sharedCounter = new AtomicInteger(0);

// later

// this operation is atomic so all threads wait for it to complete before incrementing it
sharedCounter.incrementAndGet();
```

## Deadlocks

```kotlin
data class Human(val name:String) {
    @Synchronized fun sayHi(to: Human){
        println("$name saying hi to ${to.name}")
        Thread.sleep(500)
        to.sayHiBack(this)

    }
    @Synchronized fun sayHiBack(to: Human){
        println("$name saying hi back to ${to.name}")
    }

}
fun main() {
    val adam = Human("adam")
    val eve = Human("eve")
    val adamThread = Thread {
        adam.sayHi(eve)
    }.apply {
        start()
    }

    val eveThread = Thread {
        eve.sayHi(adam)
    }.apply {
        start()
    }
    adamThread.join()
    eveThread.join()
}
```

* Adam's thread starts and locks the Adam object
* Eve's thread starts and locks the Eve object
* Then Adam's object wakes up from the `500`ms `sleep` and calls Eve's `sayHiBack` method
* Eve's object does the same thing - so they are just "stuck" waiting for each other to say hi back forever
