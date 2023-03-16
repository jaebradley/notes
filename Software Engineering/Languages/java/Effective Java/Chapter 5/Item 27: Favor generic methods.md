# Item 27: Favor generic methods

* Recursive type bounds are when a type parameter is bounded by some expression involving that type parameter itself
  * Most common use case is in connection with the `Comparable` interface
* In practice, nearly all types can be compared only to elements of their own type
* Methods might take a list of elements that implement `Comparable` to sort the list, search it, calculate min / max
  * To implement these actions, every element in the list must be comparable to every other element in the list

```java
public static <T extends Comparable<T>> T max(List<T> list)
```

* `<T extends Comparable<T>>` basically stands for "every type T that can be compared to itself"
