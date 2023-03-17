# 5.1 Synchronized collections

* Initial example checks the last index of a `Vector` via the `size` method and then calls `get` on the `Vector` with the index
  * Not thread safe, since one thread could remove an element from the list in-between when another thread is calling `size` and `get`, triggering an `IndexOutOfBoundsException`
* Synchronized collections classes guard each method with the lock on the synchronized collection object itself
* By acquiring the collection lock, can avoid any other thread modifying the collection, ensuring the `size` doesn't change
* A problem may occur when iterating over the `Vector` in a multi-threaded environment - if other threads modify the `Vector` than the iteration may fail because the size of the `Vector` may change
  * Can be addressed by holding the `Vector` lock for the duration of the iteration
  * However, this has scalability cost associated with it, since no other threads can access it at all during the iteration

## 5.1.2 Iterators and `ConcurrentModificationException`

* The iterators returned by the synchronized collections are not designed to deal with concurrent modification and they are fail-fast meaning that if they detect that the collection has changed since iteration began they throw the unchecked `ConcurrentModificationException`
  * Implemented by associating a modification count with the collection
  * If the modification count changes during iteration, `hasNext` or `next` throws `ConcurrentModificationException`
  * Check is doen without synchronization so there is a risk of seeing a stale value of the modification count and therefore the iterator does not realize that a modification has been made
* An alternative to locking the collection during iteration is to clone the collection and iterate the copy instead
  * Since the clone is confined to the executing thread, no other thread can modify it during iteration
  * The collection must be locked during the clone operation itself

## 5.1.3 Hidden iterators

* Example shows string concatenation of a `Set`, which the compiler turns into a call to `StringBuilder.append(Object)`, which in turn invokes the collection's `toString` method, and the implementation of `toString` iterates the collection and calls `toString` on each element to produce a nicely formatted representation of the collection's contents
* This string concatenation could throw `ConcurrentModificationException` because the collection is being iterated by `toString`
  * The lock on the set should be acquired before the string concatenation
* The greater the distance between the state and the synchronization that guards it, the more likely it is that someone will forget to use proper synchronization when accessing that state
* Iteration is also indirectly invoked by `hashCode`, `equals`, `containsAll`, `removeAll`, `retainAll`
