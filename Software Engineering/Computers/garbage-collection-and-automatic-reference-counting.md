# [Garbage Collection And Automatic Reference Counting](https://medium.com/computed-comparisons/garbage-collection-vs-automatic-reference-counting-a420bd4c7c81)

## Garbage Collection

* Every object tree must have one or more root objects
* Local variables are kept alive by the stack of a thread and hence GC roots
* Active Java threads are always considered live objects and are therefore GC roots
* Mark-and-sweep algorithm
  * The algorithm traverses all object references, starting with the GC roots, and marks every object found as alive
  * All of the heap memory that is not occupied by marked objects is reclaimed
    * It is simply marked as free, essentially swept free of unused objects
    * When objects are no longer referenced directly or indeirectly by a GC root, they will be removed
* GC has disadvantages like consuming additional memory to run those algorithms and this has a performance impact
* Garbage collection isn't instantaneous - it takes time

## Automatic Reference Counting

* When the reference count to each object drops to zero, the object is definitely unreachable and can be recycled
* During compile time, retain and release messages are inserted, which increase and decrease the reference count at runtime, marking for deallocation of objects when the number of references to them reaches zero
* Unlike garbage collection, it isn't a background process and it removes the objects asynchronously at runtime
* A retain cycle is when two objects keep a reference to each other and neither can be released
* ARC introduces storage modifiers which specify how the reference will behave
  * `strong` references will force the object to stay alive until the reference is removed
  * If a reference is marked as `weak` the reference will not keep the object alive and if all other references to the object go away, the object will be freed and the reference will be set to `null`l
* To avoid the previous "retain cycle", the parent would maintain a `strong` reference to the child object while the child object would have a `weak` reference tot he parent
  * When the parent object is freed, the references to the children won't prevent it from being freed
