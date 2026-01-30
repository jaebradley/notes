# 11.4.1 Narrowing lock scope

* Move code that doesn't require the lock out of `synchornized` blocks
* Example in book is a `synchronized` method where only a portion of the implementation within the method needs the lock

```java
@GuardedBy("this")
private final Map<String, String> attributes = new HashMap<String, String>();

public synchronized boolean userLocationMatches(String name, String regexp) {
  String key = "users" + name + "location"
  // Only code that needs the lock
  String location = attributes.get(key);
  if (location == null) {
    return false;
  } else {
    return Pattern.matches(regexp, location);
  }
}
```

* Below implementation only holds lock for the call to `attributes.get` reducing the time that the lock is held

```java
@GuardedBy("this")
private final Map<String, String> attributes = new HashMap<String, String>();

public boolean userLocationMatches(String name, String regexp) {
  String key = "users" + name + "location"
  // Only code that needs the lock
  String location;
  synchronized (this) {
    location = attributes.get(key); 
  }
  if (location == null) {
    return false;
  } else {
    return Pattern.matches(regexp, location);
  }
}
```

* Implementation can be improved further by delegating thread safety to a thread-safe `Map`, eliminating need for explicit synchronization
