# 11.4.3 Lock striping
* Splitting a heavily contended lock into two is likely to result in two heavily contended locks
* Lock striping is partition-based locking on a variable-sized set of independent objects
* `ConcurrentHashMap` uses an array of 16 locks
  * Each lock guards a hash bucket
* Assuming a well-distributed hash function and that keys are accessed uniformly, this should reduce the demand for any given lock by a factor of 16
* Locking the collection for exclusive access is more difficult and costly than with a single lock
* When `ConcurrentHashMap` needs to expand the map and rehash the values into a larger set of buckets, it acquires all the locks in the stripe set

## Example Striped Hash Map

* `get` acquires a single bucket lock
* `clear` needs to acquire all the locks, but not simulatenously

```java
@ThreadSafe
public class StripedMap {
  private static final int N_LOCKS = 16;
  private final Node[] buckets;
  private final Object[] locks;

  public StripedMap(int numBuckets) {
    buckets = new Node[numBuckets];
    locks = new Object[N_LOCKS];
    for (int i = 0; i < N_LOCKS; i++) {
      locks[i] = new Object();
    }
  }

  public Object get(Object key) {
    int hash = hash(key);

    synchronized (locks[hash % N_LOCKS]) {
      for (Node m = buckets[hash]; m != null; m = m.next) {
        if (m.key.equals(key)) {
         return m.value;
        }
      }
    }
    return null
  }

  public void clear() {
    for (int i = 0; i < buckets.length; i++) {
      synchronized (locks[i % N_LOCKS]) {
        buckets[i] = null;
      }
    }
  }
}
```
