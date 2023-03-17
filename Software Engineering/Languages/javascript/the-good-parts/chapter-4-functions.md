# Functions

* Functions are objects
* Linked to `Function.prototype`
* Function created two additional hidden properties
  * it's context
  * it's implementation
* Function also has a `prototype` property whose value is
  * an object with a `constructor` property whose value is
    * a function
      * this function is different than the link to `Function.prototype`

## Function Literal

* The function literal has four parts
  * The reserved word, `function`
  * An optional name - this function name is important when calling a function recursively and also for debuggers
  * Set of zero or more parameters, wrapped in parentheses - initialized to the arguments supplied when function is invoked
  * Set of statements, wrapped in curly braces that represents the function body
* Inner function enjoys access to its parameters and variables
* Inner function also enjoys access to the parameters and variables of the functions it is nested within

## Invocation

* Function invocation suspends execution of the current function, passing control and parameters to the new function
* Function receives two additional parameters: `this` and `arguments`
  * The value of `this` is determined by the invocation pattern
* If too few arguments are specified, and the argument values will be initialized to `undefined`
* If too many arguments are specified, the extra arguments will be ignored

### Method Invocation Pattern

* A method is a function that is stored as a property of an object
* When a method is invoked, `this` is bound to that object

```javascript
var example = {
  value: 0,
  increment: function(amount) {
    this.value += typeof amount === 'number' ? amount : 1;
  },
};

example.increment() // increments value by 1
example.value // is 1

example.increment(2) // increments value by 2
example.value // is 3
```

### Function Invocation Pattern

* Occurs when a function is not the property of an object
* `this` is bound to the global object
* This means that a method (i.e a function that is a property of an object) cannot use an inner function to do work because the inner function's `this` is bound to the global object while the method's `this` is bound to the containing object
* Workaround is to assign a `that` variable in the method that the inner function then references

```javascript
myObject.foo = function() {
  var that = this;

  var bar = function() {
    console.log(that.bar);
  }

  bar();
};
```

### Constructor Invocation Pattern

* JavaScript is a prototypal language meaning that it is class-free
* JavaScript is not confident in its prototypal nature so it offers an object-making syntax that is reminiscent of class-based languages
* If a function is invoked with the `new` prefix then a new object will be created with a hidden link to the value of the function's `prototype` member, and `this` will be bound to that new object

```javascript
// Creates a constructor
var Quo = function(status) {
  this.status = status;
};

// Gives all instances of Quo a public method to get the status property
Quo.prototype.getStatus = function() {
  return this.status;
};

// Make an instance of Quo
var myQuo = new Quo('confused');

myQuo.getStatus(); // confused
```

### Apply Invocation Pattern

* Functions have methods - one of these methods is the `apply` method
* The `apply` method takes two parameters
  * The first parameter specifies the value of `this` to use
  * The second parameter is an array of parameters that will be used when invoking the function

```javascript
var seven = add.apply(null, [3, 4]);

var status = {
  status: 'ok',
};

// Remember the Quo.prototype.getStatus method?
// We can call that using the apply method and the new status object
// The "this" in the case of getStatus points to the status object

Quo.prototype.getStatus.apply(status); // ok
```

## Arguments

* The other parameter that functions have access to is `arguments`
* `arguments` is an array-like collection of all the parameters that the function was called with
  * Has a `length` property but does not have any array methods
  *

```javascript
// Function that adds all its parameters

var sum = function() {
  var i, sum = 0;
  for (i = 0; i < arguments.length; i += 1) {
    sum += arguments[i];
  }
  return sum;
};

sum(1, 2, 3, 4, 5) // 15
```

## Scope

* JavaScript does not have block scope (i.e. variables in a block scope are visible outside the block)
* JavaScript has function scope
  * Variables in a function are not visible outside the function
  * Variables anywhere in a function *are* visible everywhere within the function
  * This means that it is best to declare variables used in a function at the top of the function body

```javascript
var foo = function() {
  var a = 1, b = 2;

  // a is 1, b is 2

  var bar = function() {
    // only get here when bar is invoked

    var b = 3, c = 4;
    // a is 1, b is 3, and c is 4
    // note that b from outer scope was not reassigned

    a += b + c;
    // a is 8, b is 3, and c is 4
  };

  // bar has not been invoked yet so
  // a is 1, b is 2, c is undefined

  bar();

  // a is 8, b is 2, c is undefined
}
```

## Closure

* Function's have access to the context in which they were created - this is called closure
* Function's have access to the parameter itself - not a copy

```javascript
// myObject is immediately initialized as the function is called immediately
// myObject's value variable can only be modified via the increment method

var myObject = (function() {
  var value = 0;

  return {
    increment: function(amount) {
      return value += typeof amount === 'number' ? amount : 1;
    },
    getValue: function() {
      return value;
    },
  };
}());
```

```javascript
var addEventHandlers = function(nodes) {
  var i;
  for (i = 0; i < nodes.length; i += 1) {
    nodes[i].onClick = function(e) {
      // this will always be nodes.length - 1 because when it is invoked
      // it will point to the variable i
      // which has finished looping
      alert(i);
    };
  }
};

var betterAddEventHandlers = function(nodes) {
  // helper has the correct function scope such that i will be the current value when it's invoked
  var helper = function(i) {
    return function(e) {
      alert(i);
    };
  };

  var i;
  for (i = 0; i < nodes.length; i += 1) {
    nodes[i].onClick = helper(i);
  }
};
```

## Module

* A module is a function or object that is an interface - it hides its state and implementation
* Here's a trivialized example - let's augment the `String.prototype` with a `deentitytify` method that replaces HTML entities
  * We will need a mapping of HTML entities to their counter parts
  * Where to store this mapping?
  * Could store as a global variable but globals are not great
  * Could put in function but adds runtime cost since it must be evaluated every time the function is invoked
  * Can use closures to advantage here

```javascript
// Note how outer function is invoked immediately
// This will assign deentitytify to the returned inner function
// This inner function uses regex to replace a matching entity (or keep the existing character if it doesn't match)
// The entities mapping is now private and can only be accessed through the deentityify method

String.method('deentitytify', function() {
  var entities = {
    quot: '"',
    lt: '<',
    gt: '>',
  };

  return function() {
    return this.replace(/&([^&;]+);/g,
      function(a, b) {
        var r = entity[b];
        return typeof r === 'string' ? r : a;
      }
    );
  };
}());
```

* General module pattern is
  * Create a function that defines private variables / functions
  * Create privileged functions that will access these variables / functions via closures
  * Return these privileged functions for public use

## Currying

* The act of currying is combining a function and arguments

```javascript
var add1 = add.curry(1);

// add1 will now add 1 to any passed in argument
add1(6); // 7
```

* JavaScript doesn't come with a built-in `curry` method so an implementation might look like

```javascript
// curry uses closures to keep context of "original" function
// which will be called by the returned function
// which combines the arguments passed to the invocation of curry
// with the arguments passed to the invocation of the function returned by the curry (whew)

Function.method('curry', function() {
  // arguments are not arrays, so need to use slice to replace with arrays
  var slice = Array.prototype.slice,
      args = slice.apply(arguments),
      that = this;

  return function() {
    // this is where the "original" function gets called with the combined arguments
    // Note how the arguments here will be for parameters passed to the invocation of the inner function
    return that.apply(null, args.concat(slice.apply(arguments));)
  };
});
```
