# [What Is Variable Hoisting: Differentiating Between `var`, `let`, and `const` in `ES6`](https://medium.freecodecamp.org/what-is-variable-hoisting-differentiating-between-var-let-and-const-in-es6-f1a70bb43d)

## `var` Declarations

* The JavaScript engine treats all variable declarations using `var` as if they are declared at the top of a functional scope, if declared in a function, or global scope, if declared outside of a function regardless of where the actual declaration occurs.

```javascript
console.log(shape); // undefined
var shape = "square";
console.log(shape); // shape
```
* You might expect an error to be thrown by the first `console` statement because `shape` hasn't been defined yet, but the JavaScript interpreter looks ahead and "hoists" all variable declarations to the top and the initialization remains in the same spot.

```javascript
var shape;
console.log(shape);
var shape = "square";
console.log(shape);
```

Another example

```javascript
function getShape(condition) {
    // shape exists here with a value of undefined
    // OUTPUT : undefined
    console.log(shape);
    if (condition) {
        var shape = "blue";
        // some other code
        return shape;
    } else {
        // shape exists here with a value of undefined
        return false;
    }
}
```

## `let` Declarations

* `let` declares a variable with its scope being only in that code block

```javascript
function getShape(condition) {
// shape doesn't exist here
// console.log(shape); => ReferenceError: shape is not defined
if (condition) {
        let shape = "blue";
        // some other code
        return shape;
    } else {
        // shape doesn't exist here
        return false;
    }
}
```

```javascript
var shape = "square";

let shape = "rectangle";
// SyntaxError: Identifier 'shape' has already been declared
```

## `const` Declarations

* Bindings declared using `const` are treated as constants, and thus, their values cannot be changed once defined
* Every `const` declaration must be initialized

```javascript
// valid
const shape = "triangle";
// syntax error: missing initialization
const color;
// TypeError: Assignment to constant variable
shape = "square"
```

However, things get funky with objects

```javascript
const shape = {
    name: "triangle",
    sides: 3
}
// WORKS
shape.name = "square";
shape.sides = 4;
// SyntaxError: Invalid shorthand property initializer
shape = {
    name: "hexagon",
    sides: 6
}
```

This occurs because the `shape` object can be changed in terms of what it can contain, not what it's bound to.
