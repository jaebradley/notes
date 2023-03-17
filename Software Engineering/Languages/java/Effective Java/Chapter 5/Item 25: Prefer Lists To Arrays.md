# Item 25: Prefer lists to arrays

* Arrays are covariant meaning that `Sub[]` is a subtype of `Super[]` if `Sub` is a subtype of `Super`
* Generics are invariant meaning that `List<Type1>` is neither a subtype nor a supertype of `List<Type2>`

```java
// Fails at runtime
Object[] array = new Long[1];
array[0] = "foo"

// Fails at compile time
List<Object> list = new ArrayList<Long>();
list.add("foo");
```

* Arrays are "reified" meaning that arrays know and enforce their element types at runtime
* Generics are implemented by erasure which means that they enforce type constraints at compile time and discard / erase their elemenet type information at runtime
