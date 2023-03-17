# [What I learned from doing 1000 code reviews](https://medium.com/hackernoon/what-i-learned-from-doing-1000-code-reviews-fe28d4d11c71)

## Throw exceptions when things go wrong
  * Don't just return an "empty" object when something errors
  * An empty object could be an `Optional`, `null`, empty `List`, etc.
  * If a URL can't be parsed from a `String` ask yourself, "Why is the URL malformed? Is this a data issue that should be fixed upstream?"

## Avoid using `String`s everywhere

```java
void doSomething(String opType, Data data);
// opType should be an enum

String fetchWebsite(String url);
// url should have been of type URL

String parseId(Input input);
// Return type is String but parsed IDs are actually Longs like "123456"
```

* Advantage is that if string parsing and serialization is done at the fringes of the application, don't have to keep catching parsing exceptions throughout the entire application once the data is validated once
* The specific types also make more descriptive method signatures

## Unlift methods whenever possible

```java
// AVOID
CompletableFuture<T> method(CompletableFuture<S> param);
List<T> method(List<S> param);
T method(A param1, B param2, Optional<C> param3);

// PREFER
T method(S param);
T method(A param1, B param2, C param3);
```

* All the methods to avoid are using container types (like `Optional`, `List`, etc.) as method parameters
* Worse when return type is the same kind as container (i.e. single parameter is `Optional` and return type is an `Optional`)
* This pattern introduces a less flexible interface since you can't reuse method if you aren't using `Optional`s
  * However, if you built it without `Optional` input / output types, you could reuse same method but wrap it in an `Optional` to support both cases

