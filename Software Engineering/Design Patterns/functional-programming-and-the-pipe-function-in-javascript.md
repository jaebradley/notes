# [Functional Programming And The Pipe Function In JavaScript](https://medium.com/better-programming/functional-programming-and-the-pipe-function-in-javascript-c92833052057)

* A "pipe" in real-life is a tunnel for resources to go from A to B
* A pipe in functional programming is a similar concept where the output of one process to another process which accepts it as input 

```javascript
const addFour = a => a + 4;
const minusFive = a => a - 5;

// 0 + 4 - 5 + 4
const three = addFour(minusFive(addFour(0)));
```

* Implementing `pipe` involves taking a set of functions and calling them one at a time, where the previous result of applying the function is used as the argument for the current function
* The ideal API for `pipe` would be to create a set of `pipe`d functions and then call it with an initial value - so `pipe(func1, func2, func3)(someInitialValue)`
  * The `pipe` function by itself just returns a function that will take an initial value
  * Until the function is assigned an initial value it won't execute (i.e. the underlying `reduce` won't run)

```javascript
const pipe = (...funcs) => initialValue => {
  return funcs.reduce((result, func) => {
    return func(result);
  }, initialValue);
};
```