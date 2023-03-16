# [Practical Functional Programming](https://medium.com/hackernoon/practical-functional-programming-6d7932abc58b)

* Following function composes well as it can be combined with another function without needing to make any changes to original code

```java
String addFooter(String message) {
  return message.concat(" - Sent from LinkedIn");
}

boolean validMessage(String message) {
  return characterCount(addFooter(message)) <= 140;
}
```

* Example of function that doesn't compose well because of how it returns `null`

```java
String firstWord(String message) {
  String[] words = message.split(' ');

  if (words.length > 0) {
    return words[0];
  } else {
    return null;
  }
}
```

* In order to avoid `NPE`, anything using `firstWord` could change to expect / handle a `null` value, but if components are constantly needing to be modified every time something is added, it's not really composable
* Java 8 uses `Optional`s as an alternative for handling the `null` / empty case
* Values are easier to compose than functions
  * Example of functions being composed together is nested callbacks which can turn into callback hell
  * Value-based alternative is using a `Promise`
* Iterating over a list / looping can highlight how imperative programming can only provide one of modularity or performance
  * Imagine function that loops through all values in a list and appends an `!`
  * If called twice, all values in loop would be iterated over twice
  * Instead, a `Stream` can be used which is a lazy object that will only loop over data when it is asked for a result
  * Without something like a `Stream` to increase performance, would need to consolidate list traversals in a single place, which might might breaking apart existing functionality
  * Lazy objects can achieve both modularity and performance because traversals are deferred until end

```java
values.toStream().map(someFunctionX).map(someFunctionY).collect(Collectors.toList())
```
