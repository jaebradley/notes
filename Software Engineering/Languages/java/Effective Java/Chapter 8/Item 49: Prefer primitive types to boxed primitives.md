# Item 49: Prefer primitive types to boxed primitives

* Java has a two-part type system consisting of primitives (`int`, `double`) and reference types, such as `String` and `List`
* Every primitive type has a corresponding reference type, called a boxed primitive (`Integer`, `Double`)
* Primitives have only their values, whereas boxed primitives have identities distinct from their values
  * Two boxed primitive instances can have the same value and different identities
* Primitive types have only fully functional values, whereas each boxed primitive type has one nonfunctional value, which is `null`, in addition to all the functional values of its corresponding primitive type
* Primitives are generally more time-and-space efficient than boxed primitives

```java
new Comparator<Integer>() {
  public int compare(Integer first, Integer second) {
    return first < second ? -1 : (first == second) ? 0 : 1);
  }
}
```

* `first < second` causes the `Integer` instances to be auto-unboxed - their primitive values are extracted
* If the first value is not less than the second value, then `==` will perform an identity comparison on the two object references (since they are `Integer`)
  * If `first` and `second` refer to distinct `Integer` instances that represent the same `int` value, this comparison will return `false` and the comparator will incorrectly return `1`
  * Applying `==` to boxed primitives is almost always wrong
* Fix the comparator by assigning to local variables that store the primitive `int` values corresponding to the `Integer`s and to perform the comparision on these variables

```java
static Integer i;

public static void main(String[] args) {
  if (i == 43) {
    System.out.println("Unbelievable");
  }
}
```

* This will throw a `NullPointerException`
* `i` is an `Integer`, and like all `Object`s, it's initial value is `null`
* `i == 43` compares an `Integer` to an `int`
* When you mix primitives and boxed primitives in a single operation, the boxed primitive is auto-unboxed
* If a null object reference is auto-unboxed, it will throw an `NPE`

```java
public static void main(String[] args) {
  Long sum = 0L;
  for (long i = 0; i < Integer.MAX_VALUE; i++) {
    sum += 1;
  }
  System.out.println(sum);
}
```

* Slow because `sum` is a `Long` and not `long`
* `sum` is repeatedly boxed and unboxed, causing performance degradataion
* Use primitives in preference to boxed primitives whenever you have the choice
