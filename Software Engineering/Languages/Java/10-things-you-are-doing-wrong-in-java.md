# [10 Things You're Doing Wrong In Java](https://towardsdatascience.com/10-things-youre-doing-wrong-in-java-7608e2f050c7)

* Not using `equals`
  * `==` compares memory addresses and not their content (like in the case of two `String`s)
  * Pretty much never use `==`
* String concatentate using `StringBuilder` and not `+`
  * `String`s are immutable so using the `+` operator for string concatenation basically creates three objects - the original `String`, the `String` to append, and the result `String`
  * Using `StringBuilder` removes the intermediary object
* Avoid returning `null` - generally throw an exception instead
* When there is a "heavy" computation (like `Pattern.compile(somePattern)`), and that computation is stateless, instead of executing this on each method execution, can create a static field that has this data like
  * `private static final Pattern PATTERN = Pattern.compile(somePattern)`
* Don't modify collections when iterating
  * If `remove` is called on an `ArrayList` it'll throw a `ConcurrentModificationException`
  * Instead, if such a behavior is desired, iterate over the iterator for the collection and call `remove` on the iterator like

```java
for (Iterator<String> iter = words.iterator(); iter.hasNext();) {
  if (someCondition) {
    iter.remove();
  }
}
```
