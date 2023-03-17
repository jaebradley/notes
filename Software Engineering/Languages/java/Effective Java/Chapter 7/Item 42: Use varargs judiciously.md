# Item 42: Use `varargs` judiciously

* Varargs methods accept zero or more arguments
* Works by first creating an array whose size is the number of arguments passed at the call site, then putting the argument values into the array and finally passing the array to the method
* If a method requires one or more arguments (rather than zero or more), using varargs will lead to runtime failure vs. compile time failure
  * since it's likely that an `IllegalArgumentException` will be thrown from an internal validity check, but calling the method with no arguments compiles
* Instead of using plain old varargs, define a method that takes two parameters, one "normal" parameter of the specified type, and one varargs parameter of the same type
  * `min(int firstArg, int... remainingArgs)`
* Exercise care when using the varargs facility in performance-critical situations
  * Every invocation of a varargs method causes an array allocation and initialization
* If the vast majority of calls of a varargs method have three or fewer parameters, then declare five overloadings of the method, each with zero through three parameters, and a single varargs method
  * This is so you'll pay the cost of array creation in the vast minority of all invocations
