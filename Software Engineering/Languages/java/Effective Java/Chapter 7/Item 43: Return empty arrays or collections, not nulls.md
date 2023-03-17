# Item 43: Return empty arrays or collections, not nulls

* It is possible to return the same zero-length array from every invocation that returns no items because zero-length arrays are immutable, and immutable objects may be shared freely

```java
private static final Cheese[] EMPTY_CHEESE_ARRAY = new Cheese[0];

public Cheese[] getCheeses() {
  // someCheeses is a List
  return someCheeses.toArray(EMPTY_CHEESE_ARRAY);
}
```

* An empty-array constant is passed to the `toArray` method to indicate the desired return type
* Normally, the `toArray` method allocates the returned array, but if the collection is empty, it fits in the zero-length input array
  * `Collection.toArray(T[])` guarantees that the input array will be returned if it is large enough to hold the collection, so it should never allocate an empty array
* Immutable empty collections can be returned via the `Collections.emptySet`, `emptyList`, methods
