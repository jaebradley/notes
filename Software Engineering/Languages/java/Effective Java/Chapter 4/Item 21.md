# Item 21: Use function objects to represent strategies

* Java does not provide function pointers, but object references can be used to achieve a similar effect
* It is possible to define an object whose methods perform operation on other objects, passed explicitly to the methods
* An instance of a class that exports exactly one such method is effectively a pointer to that method (these instances are known as "function objects")

```java
class StringLengthComparator {
  public int compare(String s1, String s2) {
    return s1.length() - s2.length();
  }
}
```

* A reference to the `StringLengthComparator` serves as a function pointer to the comparator
  * In other words, the `StringLengthComparator` is a "concrete strategy" for string comparison
* As is typical for concrete strategy classes, the `StringLengthComparator` class is stateless: it has no fields, hence all instances of the class are functionally equivalent
  * Thus it should be a singleton to save on unnecessary object creation
* The `Strategy` interface for the `StringLengthComparator` is (which comes from `java.util`)

```java
public interface Comparator<T> {
  public int compare(T t1, T t2);
}
```

* Concrete strategy classes are often declared using anonymous classes, but note that anonymous classes will create a new instance each time the call is executed so if it needs to be executed repeatedly, consider storing the function object in a `private static final` field and reusing it
