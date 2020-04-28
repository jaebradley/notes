# [Curry Or Partial Application](https://medium.com/javascript-scene/curry-or-partial-application-8150044c78b8)

* Partial application is the process of applying a function to some of its arguments
  * The partially applied function gets returned for later use
  * Basically, a function that takes a function with multiple parameters and returns a function with fewer parameters
  * Fixes one or more arguments inside the returned function
  * The returned function takes the remaining parameters as arguments in order to complete the function application
* Curry - a function that takes a function with multiple parameters as input and returns a function with exactly one parameter
  * A curried function always returns another function with an arity of 1 until all of the arguments have been applied
* Promises are examples of containers (similar to functions - which basically mean "something that can be mapped over") which provide a standardized way of processing data inside the container, regardless of the data's type
  * Dealing with containers instead of the values inside of containers means that you can build many generic functions that work universally for anything that fulfuills the container's interface
* Curried functions have a built-in iterator mechanism - a curried function will partially apply one argument at a time, never doing more work than it can do with a single argument
  * Remember that curried functions don't have to be finally applied immediately

```javascript
const next = a(b);
doSomeStuff().then(() => next(c));
```