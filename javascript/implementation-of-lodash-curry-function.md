# [Implementation of lodash curry function](https://medium.com/@kj_huang/implementation-of-lodash-curry-function-8b1024d71e3b)

* The basis of the curry function is an arguments collector (which makes sense since part of the behavior of curry is keeping track of all the arguments that have been specified)

```javascript
function fn() {
  console.log(arguments);
  return fn.bind(null, ...arguments);
}

let boundFn = fn(1); // 1
boundFn = fn(2); // 1, 2
boundFn = fn(3); // 1, 2, 3
```

* What's happening above is a simple implementation of an arguments collector
  * The returned function is bound function of itself where the arguments of that bound function are the arguments provided to the current function
* Can take advantage of the `length` property that JavaScript functions have, which specifies the number of arguments expected by a function
* Use closure to keep reference to "target" function (i.e. the function we want to execute once all arguments are collected)

```javascript
function curry(targetFn) {
  const totalArgumentsCount = targetFn.length;
  
  if (arguments.length <= totalArgumentsCount) {
    return curry.bind(null, ...arguments);
  }

  return targetFn.apply(null, Array.prototype.slice.call(arguments, 1));
}
```

* Idea is to use the curry function as the arguments collector - so while all arguments are being collected, another function will be returned with the preceding arguments bound to it until all arguments are collected and then the "target" function is executed
* Note that the target function is the _first_ argument to the curry function so the target function should only be executed when total number of arguments is *greater than* the length of the target function
  * Similarly, also note that when applying the arguments to the target function, the first argument is removed as the first argument is the target function (when it was passed in originally to the curry function)
