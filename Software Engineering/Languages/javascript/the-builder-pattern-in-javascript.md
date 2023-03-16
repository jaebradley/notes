# [The Builder Pattern in JavaScript](https://medium.com/better-programming/the-builder-pattern-in-javascript-6f3d85c3ae4a)

* The Builder pattern is generally needed most when the construction of complex objects needs to be simplified
  * For example, a constructor with many arguments where the caller doesn't remember what arguments align with what values (though ES6 named parameters using an object literal can be used to handle this scenario)
* So create an `XBuilder` that takes the required parameters in it's `constructor` and has setters for optional parameters and then a `build` method that generates an `X` instance (or errors if required parameters are not included)
  * Also means that any instantiation is more readable
* Imagine a `Car` class with four wheels, and a steering wheel, and brakes, etc. How to add additional features like a sunroof?
  * One solution might suggest subclassing where each subclass covers different feature sets, but this causes hierarchy to grow
  * Another approach involves a very large constructor that handles all variations, but this creates a large constructor that is hard to comprehend and maintain
