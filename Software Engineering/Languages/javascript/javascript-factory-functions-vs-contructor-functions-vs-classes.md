# [JavaScript Factory Functions vs. Constructor Functions vs. Classes](https://medium.com/javascript-scene/javascript-factory-functions-vs-constructor-functions-vs-classes-2f22ceddf33e)

* In JavaScript, any function can return a new object
  * When it's not a constructor function or clas, it's called a factory function
* Drawbacks of constructors and classes
  * Refactoring constructors or classes to use factory function is a breaking change
    * These refactors are fairly common
  * Encourages use of `instanceof`
    * `instanceof` simply checks an object's `[[Prototype]]` object against the `Constructor.prototype` property
    * Fails if `Constructor.prototype` is modified
* Benefits of classes
  * Convenient syntax
  * Familiar to people from class-based language
* Classes tend to lead to problematic class hierarchies
* Benefits of factory function
  * `this` behaves like you'd expect
  * No `instanceof`

