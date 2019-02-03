# [Beware of Chaining Array Methods in JavaScript](https://medium.freecodecamp.org/beware-of-chaining-array-methods-in-javascript-ef3983b60fbc)

* Array methods (like `forEach`) get drastically worse as the size gets larger
* Example code with 10 elements executed 6 million operations per second
* Example code with 100,000 elements executed 223 operations per second
* Instead, rewriting code using single `for` loop produces more performant code
* Functional chaining means potentially iterating over same element multiple times
* One big performance win that JavaScript compilers do is in-lining function calls (i.e. replacing the function call with the actual function itself)
