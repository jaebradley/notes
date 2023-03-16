# [Understanding JavaScript: Scope](https://medium.com/hackernoon/understanding-javascript-scope-1d4a74adcdf5)

* Global, functional, and block scope
* Can overwrite a global variable inside a function and use the new value inside function but outside function the variable is still the global value
* Nested functions can use variables in parent context
  * Will keep increasing scope by one level until it gets to global scope and if it cannot find variable then a `ReferenceError` is thrown
* Hoisting is behavior where functions and values can be referenced "before" they're declared
  * JavaScript engine does multiple passes through code - it declares variables and functions in these runs
  * Function declarations (and their definitions) are pulled to top of scope
  * Variable declarations (but _not_ their definitions) are pulled to top of scope
    * Value assignment stays where it is (value of variable will be `undefined` before value assignment)
  * For a function expression (`var test = function()`) only the declaration gets pulled to top while assignment of function stays put
  * `const` or `let` are not hoisted

