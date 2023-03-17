# [Functional Programming in JavaScript - With Practical Examples (Part 1)](https://medium.com/free-code-camp/functional-programming-in-js-with-practical-examples-part-1-87c2b0dbc276?)

* Any class that stores a value and implements a `map` method is called a `Functor` (like an array)

```javascript
class CustomFunctor {
  constructor(value) {
    this.value = value;
  }

  map(fn) {
    return new CustomFunctor(fn(this.value));
  }
}

let exampleFunctorInstance = new CustomFunctor(1);
exampleFunctorInstance.map(someFunction);
```

* `Monad`s are functors in that they have a `map` method but also implement other methods like `apply` and `chain` methods
  * Often times they have an `of` static constructor, a `join` method that returns the underlying value, a `chain` method that applies a `map` and a `join` at the same time, and an `apply` method that maps a given monad's value to the passed in monad

```javascript
class ExampleMonad {
  constructor(value) {
    this.value = value;
  }

  static of(value) {
    return new ExampleMonad(value);
  }

  map(fn) {
    // Applies function and returns result as value of
    // another Monad
    return ExampleMonad.of(fn(this.value));
  }

  join() {
    return this.value;
  }
  
  chain(fn) {
    // Helper function that maps passed in function
    // And pulls value out
    return this.map(fn).join();
  }

  apply(otherMonad) {
    return otherMonad.map(this.value);
  }
}
```

* `Maybe` monad is a class that takes care of `null` or `undefined` values
  * Specifically, if monad's value is `null` or `undefined`, the `map` method won't execute, thereby avoiding things like NPE
  * Ramda uses two classes `Just` or `Nothing` under the hood to signify non-`null` / `null` values, respectively
* Currying converts a function that takes multiple parameters into a function that takes a single parameter at a time - won't run function until all parameters are passed
* Use `Either` monads to deal with multiple function calls where fast-failing functions is preferred
  * `Either` monad has `Left` and `Right` sub-classes - `Left` stores errors while `Right` stores useful values
  * Both `Left` and `Right` have `map`, `chain`, etc. but `Left` constructor doesn't apply methods whereas `Right` will implement methods that apply values
  * Ramda uses an `either` method that takes a success handler, and error handler, and an `Either` monad
  * Depending on whether the `Either` monad is a `Right` or a `Left`, the relevant success / error handler will be called
