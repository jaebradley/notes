# Item 41: Use overloading judiciously

```java
public class CollectionClassifier {
  public static String classify(Set<?> s) {
    return "Set";
  }

  public static String classify(Collection<?> c) {
    return "Unknown Collection";
  }

  public static void main(String[] args) {
    Collection<?>[] collections = {
      new HashSet<String>(),
      new ArrayList<BigInteger>(),
      new HashMap<String, String().values()
    };

    for (Collection<?> c : collections) {
      System.out.println(classify(c));
    }
  }
}
```

* `classify` is overloaded and the choice of which overloading to invoke is made at compile time
* For all iterations of the loop in the `main` method, the compile-time type of the parameter is the same - `Collection<?>`
* The runtime type is different, but this does not affect the choice of overloading
* Only the `Collection<?> c` `classify` method is called
* Selection among overloaded methods is static while selection among overridden methods is dynamic
  * The correct version of an overridden method is chosen at runtime, based ont he runtime type of the object on which the method is invoked
* If an instance method is overridden in a subclass and this method is invoked on an instance of the subclass, the subclass's overriding method executes, regardless of the compile-time type of the subclass instance
* A safe, conservative policy is never to export two overloadings with the same number of parameters
* The class `ObjectOutputStream` has variants of its `write` method like `writeBoolean`, `writeInt`, `writeLong`
  * This allows for corresponding `read` methods like `readBoolean`, `readInt`, etc
* An example of confusing overloading is `List`'s `remove` method
  * `remove(int)` vs. `remove(E)` - the former will remove at the specified index while the latter will remove the specified object
  * So example wants to remove a value, `1`, but has to remove it like `list.remove((Integer) i)` to call the correct overloaded method
