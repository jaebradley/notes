# Chapter 3: Functions

* The first rule of functions is that they should be small - the second rule of functions is that they should be smaller than that
  * Should be 2, 3, maybe 4 lines long
* Functions should do one thing, they should do it well, and they should do it only
* Example function does multiple things - like determining whether the page is a test page, and if so, doing setup and teardown, and then rendering the page in HTML
* Another way of knowing if a function is doing more than one thing is if you can extract a function from it with a name that is different than a restatement of its implementation
* If a function looks like it has different sections like "declaration", "initializations" then this is probably a symptom that functions are not doing one thing
* The "Stepdown Rule" uses `To` statements to detect different abstraction levels
  * Each `To` statement should indicate one level of abstraction
```
To include the setups and teardowns, we include setups, then we include the test page...
  To include the setups, we include the suite setup...
    To include the suite setup...
```

* You know you are working on clean code when each routine turns out to be pretty much what you expected
* A long descriptive name is better than a short enigmatic name
  * A long descriptive name is better than a long descriptive comment
* There are two main reasons to pass a single argument into a function
  * Asking a question about the argument like `fileExists` or operating on that argument, transforming it, and then returning it
* If the function is a `void` function, it might operate on some `event` and alter the state of the system given some `event`
* If a function is going to transform its input argument, the transformation should appear as the return value
* Passing a boolean into a function is a smell that the function does more than one thing (it does one thing when the boolean "flag" is `true` vs. `false`)
* When a function seems to need more than two or three arguments, it is likely that some of those arguments ought to be wrapped into a class of their own
  * `makeCircle(double x, double y, double radisu)` => `makeCircle(Point center, double radius)` where `Point` encompasses the `x` and `y` values
* In the case of a monad, the function + argument should from a nice verb/noun pair
  * `write(name)` could probably be better named as `writeField(name)`, which would indicate that `name` might refer to a field
* Side-effects in a function might be making unexpected changes to variables in its own class, or change some type of global state
* In the code example, there is a `checkPassword` function that initializes a session
  * The name does not imply that a session will be initialized
  * The session initialization creates a temporal coupling where `checkPassword` can only be called when it is safe to initialize a session
  * If `checkPassword` is called when it is _not_ safe to initialize a session then session data might be lost
  * If temporal couping must exist, at least expose it in the name (so `checkPasswordAndInitializeSession`)
* Functions should either do something or answer something but not both i.e. either the function should change the state of an object or it should return some information about that object

```java
public boolean set(String attribute, String value);

if (set("key", "value")) {
  // etc
}
```

* It's unclear what `set` is doing / verifying (via returning a `boolean`)
* Instead, it would be clearer if there was a `hasAttribute` that would return a `boolean` if the object does not have the attribute, and then a `setAttribute` that would be `void` but would do the actual attribute updating
