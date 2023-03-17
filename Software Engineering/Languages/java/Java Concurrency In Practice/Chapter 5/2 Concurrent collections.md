# 5.2 Concurrent collections

## 5.2.1 `ConcurrentHashMap`

* Instead of synchronizing every method on a common lock, restricting access to a single thread at a time, it uses a finer-grained locking mechanism called lock striping to allow a greater degree of shared access
* Arbitrarily many reading threads can access the map concurrently, readers can access the map concurrently with writers, and a limited number of writers can modify the map concurrently, resulting in higher throughput under concurrent access
* Has weakly consistent iterators, and traverse elements as they existed when the iterator was constructed, and may (but is not guaranteed to) reflect modifications to the collection after the construction of the iterator
* The result of `size` could be out of date by the time it is computed, it is really only an estimate
  * In reality, methods like `size` and `isEmpty` are less useful in concurrent environments because these quantitites are moving targets
* Unable to acquire a `ConcurrentHashMap` for exclusive access unlike `Hashtable` and `synchronizedMap` to prevent any other thread from accessing it
  * So unable to use `ConcurrentHashMap` to do client-side locking - many compound operations like put-if-absent are implemented as atomic operations and specified by the `ConcurrentMap` interface

## 5.2.3 `CopyOnWriteArrayList`

* Copy-on-write collections derive their thread safety from the fact that as long as an effectively immutable object is properly published, no further synchronization is required when accessing it
  * Immutability is implemented by creating and republishing a new copy of the collection every time it is modified
* Iterators for the copy-on-write collections retain a reference to the backing array that was current at the start of the iteration, and since this will never change, they need to synchronize only briefly to ensure visibility of the array contents
* Copy-on-write collections are reasonable to use only when iteration is far more common than modification (event-notifaction systems, where delivering a notification requires iterating over a list of registered listeners)
