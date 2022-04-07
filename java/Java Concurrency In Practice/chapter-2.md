# Chapter 2

## Atomicity

```java
public void service(ServletRequest req, ServletResponse resp) {
  BigInteger i = extractFromRequest(req);
  BigInteger[] factors = factor(i);
  ++count;
  encodeIntoResponse(resp, factors);
}
```

* The increment operation is not atomic, which means it does not execute as a single indivisible operation
  * Shorthand for three discrete operations - fetch the current value, add one to it, and write the new value back
  * If there are two threads running this logic, both threads could read the value as, say, `9`, and both add `1` to it, setting the `count` as `10`, instead of technically being `11`

## Race Conditions In Lazy Initialization

```java
public ExpensiveObject getInstance() {
  if (instance == null) {
    instance = new ExpensiveObject();
  }

  return instance;
}
```

* If two threads are executing `getInstance` at the same time and they both see that `instance` is `null` then the two callers of `getInstance` will receive different results, even though the intention of `getInstance` is to return the same instance

## Compound Actions

* The `java.util.concurrent.atomic` package contains atomic variable classes like `AtomicLong` that ensure all actions that access the value are atomic

```java
private final AtomicReference<BigInteger> lastNumber = new AtomicReference<BigInteger>();
private final AtomicReference<BigInteger[]> lastFactors = new AtomicReference<BigInteger[]>();

public void service(ServletRequest req, ServletResponse) {
  BigInteger i = extractFromRequest(req);
  if (i.equals(lastNumber.get()) {
    encodeIntoResponse(resp, lastFactors.get());
  } else {
    BigInteger[] factors = factor(i);
    lastNumber.set(i);
    lastFactors.set(factors);
    encodeIntoResponse(resp, factors);
  }
}
```

* In the above logic, the most recently computed result is cached in case two consequctive clients make a request for the factorization of the same number
* Even though both values are atomic, because the two values have related meanings / logic (i.e. they are _not_ independent), when updating one, all the others must be updated in the _same atomic operation_
* There is still a window of vulnerability where `lastNumber` and `lastFactors` where one has been modified and the other has not so during that time other threads could make changes
  * Similarly, the two values cannot be fetched simultaneously - between the time when thread A has fetched the two values, thread B could have changed them, and thus thread A's values are not accurate

## Intrinsic Locks

* A `synchronized` block has two parts - a reference to the object that it will be locking, and some block of code to be guarded by that lock
* Every Java object can implicitly act as a lock
* The lock is automatically acquired by the executing thread before entering a synchronized block and automatically released when control exits the `synchronized` block (including when an exception is thrown)
* These "intrinsic" locks are mutexes (i.e. mutually exclusive locks) which means that only one thread (at most) can own the lock
  * If thread A attempts to acquire a lock held by thread B, thread A must wait / "block" until thread B releases the lock
* Since only one thread at a time can execute a block of code guarded by a given lock, the `synchronized` blocks guarded by the same lock execute automatically with respect to one another

## Reentrancy

* Intrinsic locks are reentrant meaning that if a thread tries to acquire a lock that it already holds, the request succeeds
  * Locks are acquired on a per-thread basis versus a per-invocation basis
* This reentrancy behavior is implemented by the JVM recording the owner and the invocation count
  * When a thread acquires an unheld lock, the invocation count is set to `1`
  * If the same thread acquires the lock again, the count is incremented
  * When the thread exits the `synchronized` block the count is decremented
  * When the count reaches `0` the lock is released
* Most relevant in situations where a subclass overrides a `synchronized` method and then calls the superclass method
  * If the reentrant logic did not exist, the superclass method  would try to acquire the lock on the instance, but the superclass method would never be able to acquire the lock because it would be held by the subclass method and the thread would be deadlocked

## Guarding State With Locks

* Synchronizing every method of a thread-safe class does not mean that compound actions on that class are atomic

```java
if (!vector.contains(element)) {
  vector.add(element);
}
```

* Even though both `contains` and `add` are atomic, there is a race condition present so need to make compound actions atomic

## Liveness And Performance

```java
public void service(ServletRequest req, ServletResponse resp) {
  BigInteger i = extractFromRequest(req);
  BigInteger[] factors = null;

  synchronized (this) {
    ++hits;
    if (i.equals(lastNumber)) {
      ++cacheHits;
      factors = lastFactors.clone();
    }
  }

  if (factors == null) {
    factors = factor(i);
    synchronized (this) {
      lastNumber = i;
      lastFactors = factors.clone();
    }
  }
  encodeIntoResponse(resp, factors);
}
```

* The first `synchronized` block guards the sequence that tests whether we can just return the cached result (in the case where the request number is the same as the previous request)
  * Because there's a hit counter and a cache hit counter, both need to updated in the same `synchronized` block
* The second `synchronized` block guards against updating the cached number and cached factors atomically
* The non-`synchronized` blocks operate on local (i.e. stack-based) variables, which are not shared between threads
* Avoid holding locks during lengthy computations or operations at risk of not completing quickly, like network or other I/O operations
