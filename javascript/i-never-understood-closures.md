# [I Never Understood Closures](https://medium.com/dailyjs/i-never-understood-javascript-closures-9663703368e8)

## What Happens When JS Calls A Function

* When JS calls a function
  * A new (local) execution context is created
  * Context will have it's own set of variables, which are local to that execution context
  * This context is thrown onto the execution stack
* When a function ends
  * Local execution context is popped off the execution stack
  * Function sends returned value (or `undefined`) back to calling context
  * Local execution context is destroyed - **all variables that were declared within the local execution context are erased**

```javascript
// Global variable, a, is defined and assigned a value of 3
let a = 3;

// Global variable, addTwo, is defined and assigned a function definition
// The code inside the function is not executed or evaluated
function addTwo(x) {
  let ret = x + 2;
  return ret;
}

// Global variable, b, is defined and assigned the return value of addTwo
// Look in global context for variable called addTwo - yep, it exists
// Look in global context for variable called a - yep, that also exists
// Create local execution context and push onto the call stack
// Inside local execution context, create a local variable called x and assign it 3
// Create new local variable, ret, and assign it the result of x + 2
// Look for x inside local execution context - yep, it exists
// ret is assigned 3 + 2 => 5
// 5 is returned and all local execution context variables are destroyed
let b = addTwo(a);
console.log(b);
```

## How Does JS Find A Variable

* Variables are either in some local context, or in the global context
* If a variable is declared in a local context and it doesn't exist in that context, JS will look for that variable in the calling context.
* It will continue "reaching" into the higher context, until it gets to the global context
* If it can't find the variable in the global context, it will assign the variable a value of `undefined`

## What Happens When JS Returns A Function

```javascript
// Declare global context variable, val, and assign it a value of 7
let val = 7;

// Declare global context variable, createAdder, and assign it a function definition
// Again, the function definition is not evaluated or executed
function createAdder() {
  function addNumbers(a, b) {
    let ret = a + b;
    return ret;
  }
  return addNumbers;
}

// Declare global context variable, adder, and assign it the returned function definition
// When createAdder gets called, enter local context
// In the local context, a local variable, addNumbers, is declared and assigned a function definition
// Again, the function definition is not evaluated or executed
// That function definition is returned and the local context is destroyed
let adder = createAdder();

// Declare global context variable, sum, and assign it the returned value from addNumbers
// Enter local context
// addNumbers creates local variables, a and b, which are assigned 7 and 8, respectively
// Local variable, ret, is declared and assigned the value of a + b
// Both a and b are found in the local context, so ret is assigned 15
// 15 is then returned and the local context is destroyed
let sum = adder.addNumbers(val, 8);
console.log('The sum is...', sum);
```

## Ok, Let's Put This All Together And Talk About Closures

```javascript
// Yeah yeah yeah, declare global context variable blah blah blah function definition #itdoesntgetevaluatedorexecuted
function createCounter() {
  let counter = 0;
  const myFunction = function() {
    counter = counter + 1;
    return counter;
  }
  return myFunction;
}

// Global context variable increment is assigned returned value of createCounter
// Enter local context
// Local context variable called counter which is assigned a value of 0
// Local context variable myFunction which is assigned a function definition
// When we return the function definition, WE ALSO CREATE A MOTHERFUCKING CLOSURE (FUCKING FINALLY) AND INCLUDE IT AS PART OF THE FUNCTION DEFINITION
// This closure represents the variables that are in scope (so counter, which has a value of 0)
// Can also think of the closure as the "backpack" of variables that were in scope when the function definition was created
const increment = createCounter();

// Look up variable increment in global context
// Enter local context
// When counter = counter + 1 is executed, before looking at local or global context, the closure is checked
// Closure contains variable named counter
// counter variable in closure gets updated to 1 after counter = counter + 1
// Returned value gets assigned to caller
// Same thing happens for each incremental (ha!) increment function call
const firstCount = increment();
const secondCount = increment();
const thirdCount = increment();
console.log('all counts:', firstCount, secondCount, thirdCount);
```
