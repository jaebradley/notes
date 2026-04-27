# [Curry And Function Composition](https://medium.com/javascript-scene/curry-and-function-composition-2c208d774983)

* A curried function is a function that takes multiple arguments one at a time
  * Given a function with 3 parameters, the curried version will take one argument and return a function that takes the next argument, which returns a function that takes the third argument
  * Last function returns the result of applying the function all of its arguments
* Partial application is a function which has been applied to some, but not yet all of its arguments
  * It's a function which has some arguments fixed inside its closure scope
* Curried functions always return a unary function - a function which takes one argument - so all curried functions return partial applications but not all partial applications are the result of curried functions
* Curried functions are useful in the context of function composition
* `compose` is a pipeline of functions where the output of one functoin connects to the input of the next

```javascript
const compose = (...fns) => x => fns.reduceRight((y, f) => f(y), x);
```

* Takes a set of functions and returns a function which takes an initial value and then iterates from the right-most argument to the left-most argument calling the previous computation as the input to the current function (starting with the initial value)
  * Because curried functions take an argument one at a time, they are very convenient when combined with `compose` (which obviously evaluates one function at a time, and passes a single argument to that function)