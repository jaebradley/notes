# [Execution Context, Lexical Environment and Closures in JavaScript](https://medium.com/better-programming/execution-context-lexical-environment-and-closures-in-javascript-b57c979341a5)

* The default context in the stack is, of course, the global execution context
* An execution context is created whenever you call a function
* A lexical environment is part of an execution context and keeps track of variables and function names

```javascript
function foo() {
  var a = 10;
  function bar() {

  }
}

// Example Execution Environment + Lexical Environment might look like

execution_environment: {
  LexicalEnvironment: {
    a: 10,
    bar: function() {

    }
  },
  ThisBinding: // some reference to the appropriate this
}
```

* A reference to the outer lexical environment is created when a lexical environment is created
  * When a value can't be found as a property in the current lexical environment, it'll look at the parent lexical environment, moving up the chain until it hits the global lexical environment (which will throw a `ReferenceError` if it still can't be found)
* The parent lexical environment is determined when the function is declared, *not when it's invoked*

```javascript
var x = 1;
function foo() {
  var x = 2;
  bar();
}

function bar() {
  console.log(x);
}

// foo will output 1 since bar's parent lexical environment is the global lexical environment and not where it's called
foo(); 
```

* Imagine a function that takes some variables, and returns a function that uses those variables in some way (very vague I know)
  * The returned function will have a new `Scope` object called a `Closure` that references values from it's surrounding execution context (even after the execution context was removed from the stack)
    * Can see this `Scope` object by `console.dir`ing the returned function and looking under the `Scopes` property
  * "A closure is the combination of a function bundled together (enclosed) with references to its surrounding state (the lexical environment). A closure gives you access to an other function's scope from an inner function." 

## Phases of Execution Context

* Global execution context and function execution context
  * Function execution context is created on function call
  * Both have a creation phase and an execution phase
* Creation phase declares variables and functions
  * Creation involves hoisting variables and functions, which means variables are assigned `undefined`