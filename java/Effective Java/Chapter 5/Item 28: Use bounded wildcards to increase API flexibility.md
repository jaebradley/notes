# Item 28: Use bounded wildcards to increase API flexibility

* Imagine a `Stack<E>` class with the following method

```java
public void pushAll(Iterable<E> src) {
  for (E e : src) {
    push(e);
  }
}
```

* If a `Stack<Number>` is created, cannot `pushAll` an `Iterable<Integer` to it since parameterized types are invariant (i.e. `List<String>` is not a subtype of `List<Object>`)
* A bounded wildcard type like `Iterable<? extends E>` means that the `Iterable` is now made up of elements that are some subtype of `E`
  * Remember that subtype is defined so that every type is a subtype of itself, even though it does not extend itself
  * Note that the `Iterable` parameter is an `E` element "producer"
* Now imagine a `popAll` method
  * Instead of taking a `Collection<E>` the `popAll` method should take a `Collection<? super E>` which means a collection of some supertype of `E`
  * Can think of the `Collection` as an `E` element consumer, as the `Stack` is popping elements into the `Collection` (i.e. `inputCollection.add(pop())`)
* For maximum flexibility, use wildcard types on input parameters that represent producers or consumers
  * If a parameterized type represents a `T` producer, use `<? extends T>`
  * If a parameterized type represents a `T` consumer, use `<? super T>`
* Do not use wildcard types as return types as this would force them to use wildcard types in client code
  * If the user of a class has to think about wildcard types, there is probably something wrong with the class's API
* Item 27 had the following `max` method

```java
public static <T extends Comparable<T>> T max(List<T> list)

// should be revised to
public static <T extends Comparable<? super T>> T max(List<? extends T> list)
```

* The `List<T>` produces `T` instances so can change the type from `List<T>` to `List<? extends T>`
* A `Comparable` consumes `T` instances and produces integers indicating order, hence the `<? super T>`
* So always use `Comparable<? super T>` since `Comparable` are always consumers (same with `Comparator`s)
* Imagine a method to swap two indexed elements in a list and the following possible method declarations for this method

```java
public static <E> void swap(List<E> list, int i, int j);
public static void swap(List<?> list, int i, int j);
```

* In a public API, the second declaration is better because it's simpler
  * Pass in any list and the method swaps the indexed elements - no type paramters to worry about
* If a type parameter appears only once in a method declaration, replace it with a wildcard
* In the case of the second `swap` declaration, need a second generic static method to help with capturing the wildcard type
