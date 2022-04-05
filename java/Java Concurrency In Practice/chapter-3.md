# Chapter 3

## Visibility

* Example of what happens when threads share data without synchronization

```java
private static class ReaderThread extends Thread {
  public void run() {
    while (!ready) {
      Thread.yield();
      System.out.println(number);
    }
  }

  public static void main(String[] args) {
    new ReaderThread().start();
    number = 42;
    ready = true;
  }
}
```

* The example could loop forever because the value of `ready` might never become visible to the reader thread
* Could print `0` because the write to `ready` might be visible to the reader thread before the write to `number` (this is called _reordering_)
* There is no guarantee that operations in one thread will be performed in the order given by the program as longas the reording is not detectable from within that thread, even if reordering is apparent to other threads
  * The Java Memory Model permits the compiler to reorder operations and cache values in registers, and permits CPUs to reorder operations and cache values in processor-specific caches
* When the main thread writes first to `number` and then to `ready` without synchronization, the reader thread could see those writes happen in the opposite order - or not at all

## Nonatomic 64-bit Operations

* The Java Memory Model permits the JVM to treat a 64-bit read or write as two separate 32-bit operations
  * If the reads / writes occur in different threads, it is possible to read a nonvolatile `long` and get back the high 32 bits of one value and the low 32 bits of another
  * It is not safe to use shared mutable `long` and `double` variables in a multithreaded program unless they are declared volatlie or guarded by a lock

## Volatile Variables

* When a field is declared `volatile` the compiler and runtime are put on notice that this variable is shared and that operations on it should not be reordered with other memory operations
* Volatile variables are not cached in registers or in caches where they are hidden from other processors - a read of a volatile variable always returns the most recent write by any thread
* From a memory visiblity perspective, writing a volatile variable is like exiting a `synchronized` block and reading a volatile variable is like entering a `synchronized` block
* Locking can guarantee both visiblity and atomicity while volatile variables can only guarantee visiblity

## Publication and Escape

* Returning a reference from a nonprivate method (even a reference to a `private` object, "publishes" that object potentially allowing internal mutable state to escape

### Safe Construction Practices

* A common mistake that can let the `this` reference escape during construction is to start a thread from a constructor
* When an object creates a thread from its constructor, it almost always shares its `this` reference with the new thread, either explicitly (by passing it to the constructor) or implicitly (because the `Thread` or `Runnable` is an inner class of the owning object)
  * Nothing wrong with creating the thread in the constructor, just don't start the thread
  * Expose a `start` or `initialize` method that will call the thread

### Stack Confinement

* Local variables are intrinsically confined to the executing thread - they exist on the executing thread's stack

### `ThreadLocal`

* `get` and `set` accessor methods maintain a separate copy of the value for each thread that uses it, so a `get` returns the most recent value passed to `set` from the currently executing thread
* Conceptually, you can think of a `ThreadLocal<T>` as holding a `Map<Thread, T>` that stores the thread-specific values, though this is not how it is actually implemented
  * The thread-specific values are stored in the `Thread` object itself - when the thread terminates, the thread-specific values can be garbage collected

## Immutability

* Immutable objects are inherently thread-safe
* Immutability is _not_ equivalent to declaring all fields `final` - an object whose fields are `final` may stil be mutable since `final` fields can reference mutable objects
* Additional property of immutability that is less obvious - the `this` reference does not escape during construction

### Final Fields

* `final` fields can't be modified (although the objects they refer to can be modified if they are mutable)

### Using `volatile` to publish immutable objects

```java
private volatile OneValueCache cache = new OneValueCache(null, null);

public void service(ServletRequest req, ServletResponse resp) {
  BigInteger i = extractFromRequest(req);
  BigInteger[] factors = cache.getFactors(i);

  if (factors == null) {
    factors = factor(i);
    cache = new OneValueCache(i, factors);
  }

  encodeIntoResponse(resp, factors);
}
```

* When a thread sets the `volatlie` `cache` field to reference a new `OneValueCache`, the new cached data becomes immediately visible to other threads
* The `cache`-related operations cannot interfer with each other because `OneValueCache` is immutable and the `cache` field is accessed only once in each of the relevant code paths

## Safe Publication

```java
public class Holder {
  private int n;

  public Holder(int n) { this.n = n; }

  public void assertSanity() {
    if (n != n) {
      throw new AssertionError("This statement is false");
    }
  }
}
```

* If the `Holder` class is published and a thread other than the publishing thread calls `assertSanity` it could throw `AssertionError`
  * `Holder` can be made immune to improper publication by declaring the `n` field to be `final`
  * While it may seem that field values set in a constructor are the first values written to those fields and therefore that there are no "older" values to see as stale values, the `Object` constructor first writes the default values to all fields before subclass constructors run
    * It is therefore possible to see the default value for a field as a stale value
    * So a thread could see a stale value the first time it eads a field and then a more up-to-date value the next time it reads the field, which is why `assertSanity` can throw `AssertionError`

## Safe Publication Idioms

* If thread A places Object X in a thread-safe collection and thread B subsequently retrieves it, B is guaranteed to see the state of X as A left it (even though the application code that hands X off in this manner has no explicit synchronization)
* Using a static initializer is often the easiest and safest way to publish objects that can be statically constructed
  * Static initializers are executed by the JVM at class initialization time; because of internal synchronization in the JVM, this mechanism is guaranteed to safely publish any objects initialized in this way
