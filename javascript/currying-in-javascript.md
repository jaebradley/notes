# [Currying In JavaScript](https://medium.com/@kevincennis/currying-in-javascript-c66080543528)

* `curry` function takes a function and returns a function
* Need to keep track of arity of the input function - can do this by getting `inputFunction.length`
* Each time curried function is called, need to keep track of these arguments in an array whose reference can be passed around via closure
* When the number of arguments in that array is equal to the number of arguments that our input function expects, we call it - otherwise we return a new function
* The way the author implemented their `curry` function, they have an IIFE where they declare an array of the arguments passed to the IIFE, named `resolver`
* This `resolver` function returns a function that copies the array of arguments
* This function that's returned is actually the function that gets called by end user, when they pass in an argument
* So when this function gets called, it pushes it's arguments to the array created by `resolver` that keeps track of all previous arguments
* After it pushes the arguments to the array of arguments in `resolver`, it checks to see if the total array of arguments is >= the arity of the original function, and if it is, it calls the original function with the arguments or it calls `resolver` with the given arguments

```javascript
function curry(fn) {
  // Keep track of input function's arity via closure
  const arity = fn.length;

  return (function resolver() {
    // keep track of previous arguments when resolver is called again recursively
    const previousArguments = Array.prototype.slice.call(arguments);
    
    // this returned function will be called by end user
    // notice how it has reference to previous arguments via closure
    return function() {
      const nextArguments = previousArguments.slice();
      
      // Here's where the arguments passed to the returned function
      // by the end user, get added to the previous arguments to
      // form the next arguments
      Array.prototype.push.apply(nextArguments, arguments);

      // If the current arguments have the same length as the input function's argument list then execute the input function
      // Else, execute the resolver function again
      // Executing the resolver function again, will copy the arguments (which are the current arguments)
      // And then return a function for the end user to call again
      const nextFunction = nextArguments.length >= arity ? fn : resolver;

      return nextFunction.apply(null, nextArguments);
    }
  })
}
```