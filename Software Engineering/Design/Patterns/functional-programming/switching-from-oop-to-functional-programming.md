# [Switching from OOP to Functional Programming](https://medium.com/@olxc/switching-from-oop-to-functional-programming-4187698d4d3)

## No Classes

* Instead of creating instances of classes or class methods, just functions calling functions
* Store data in data records which are just classes with a constructor and nothing else
  * Instead of mutating a record's properties, instead clone the record with the new value(s)
  * Some records will contain types that can be of one type or another type - author describes these as "sum" types
    * Example is a `Shape` can be a `Circle` or a `Square` - but it does not indicate inheritance

## Function Properties

* Pure - there should be no side-effects
  * Change global variables, access filesystem, execute network requests with care / should be communicated in function definition
* Total - return values for all inputs
  * Functions that throw exceptions are not total
    * Example is a `divide` function that throws an exception when dividing by `0`
  * Return an object to indicate an error state / exceptions
* Deterministic - always returns the same result for the same input
  * Functions that depend on some external state like current date or the current timezone are not deterministic
* Main benefit of adhering to properties is that you obtain referential transparency - can look at a function definition and know exactly what it can / cannot do
  * Makes it easier to refactor as it's easier to understand complexity

## Immutability

* Cannot reassign variable - instead create different variable and assign the thing you want to that variable / copy of existing variable
* Cannot use `for` loops since most `for` loops depend on mutating a counter variable
  * Instead use recursion or higher-order functions

```javascript
// Recursive approach that repeatedly calls sum function and adds first value of values (if it exists)
function sum(nums) {
  if (nums === null) {
    return 0;
  }

  return nums[0] + sum(nums.slice(1));
}

// Using higher-order reduce function to add each value to total which starts at 0
nums.reduce((value, total) => value + total, 0);
```

## Imperative vs. Functional

```javascript
function someImperativeFunction() {
  doThis();
  doThat();
  doSomethingElse();
  doFinalThing();
}
```

* Each imperative function's execution results in a changed state (files have been written, something has been outputted to console, etc)
  * Once it's executed, it's done
* In a functional program, nothing is executed until the very end
  * Functions take and return values so the output of one function is the input of another functiona nd so on and so forth
  * So functional programs detail an execution plan like "x is called first and then it's output is used to call y" and so on

```javascript
function someFunctionalFunction() {
  doFinalThing(doSomethingElse(doThat(doThis())));
}
```
