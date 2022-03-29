# Item 50: Avoid strings where other types are more appropriate

* Strings are poor substitutes for other data types
  * Translate it to `int`, `float`, `BigInteger`, `boolean`
  * If there's an appropriate value type, create one or use an existing one
* Strings are poor substitutes for enum types
* Strings are poor substitutes for aggregate types
  * If an entity has multiple components, it is usually a bad idea to represent it as a single string

```java
String compondKey = className + "#" + i.next();
```

* To access individual fields, have to parse the string (slow, tedious, error-prone)
* Can't provide `equals`, `toString`, or `compareTo`
* Better approach is to write a class to represent the aggregate

```java
public class ThreadLocal {
  // Sets the current thread's value for the named variable
  public static void set(String key, Object value);

  // Returns the current thread's value for the named variable
  public static Object get(String key);
}
```

* Problem is that the string keys represent a shared global namespace for thread-local variables
* The client-provided string keys have to be unique - if two clients independently decide to use the same name, they unintentionally share a single variable
* Instead, replace the string with an unforgeable key

```java
public static class Key{
}

// Generate a unique, unforgeable key
public static Key getKey() {
}

public static void set(Key key, Object value);
public static Object get(Key key);
```

* No need for the static methods, they can just become instance methods on the `Key`
* However, the `Key` is no longer a key for a thread-local variable - it _is_ a thread-local variable
* So make the `Key` class the `ThreadLocal` class (which is roughly the API that `java.lang.ThreadLocal` provides
