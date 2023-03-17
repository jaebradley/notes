# Item 16: Favor composition over inheritance

* A subclass depends on the implementation details of its superclass
  * The superclass's impelementation may change from release to release, and if it does, the subclass may break, even though its code has not been touched

```java
public class InstrumentedHashSet<E> extends HashSet<E> {
  private int addCount = 0;

  @Override public boolean add(E e) {
    addCount++;
    return super.add(e);
  }

  @Override public boolean addAll(Collection<? extends E> c) {
    addCount += c.size();
    return super.addAll(c);
  }
}

InstrumentedHashSet<String> s = new InstrumentedHashSet<String>();
s.addAll(Arrays.asList("Snap", "Crackle", "Pop"); // addCount is now 6, instead of the expected 3
```

* `HashSet#addAll` is implemented on top of the `add` method
* `InstrumentedHashSet` added `3` from the input collection, then called `HashSet#addAll` which called the `add` method, overridden by `InstrumentedHashSet#add`, which incremented `addCount` for each element in the collection, hence the double-counting
* Instead of extending existing class, give new class a private field that references an instance of the existing class
  * Called "composition" since existing class becomes a component of the new one
* Each instance method in the new class invokes the corresponding method on the contained instance of the existing class and returns the result (called forwarding)

```java
public class ForwardingSet<E> implements Set<E> {
  private final Set<E> s;
  public void cleaer() { s.clear(); }
  // etc
}

public class InstrumentedHashSet<E> extends ForwardingSet<E> {
  @Override public boolean add(E e) {
    addCount++;
    return super.add(e);
  }
}
```

* `InstrumentedHashSet` transforms one `Set` into another, adding the instrumentation functionality
* `InstrumentedHashSet` is also known as a wrapper or decorator
* Inheritance is only appropriate in circumstances where the subclass really is a subtype of the superclass
  * A class B should extend a class A only if an "is-a" relationship exists between the two classes - is every B really an A?
