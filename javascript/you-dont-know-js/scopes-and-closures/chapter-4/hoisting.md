# [Hoisting](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch4.md#chapter-4-hoisting)

```javascript
a = 2;
var a;
console.log(a); // 2
```

```javascript
console.log(a); // undefined

var a = 2;
```

* JavaScript thinks of variable declarations (like `var a = 2`) as two statements
  * `Engine` will compile JavaScript code before executing it
    * Part of compilation process is to find and associate all declarations in their respective scopes
  * The actual variable declaration is processed during the compilation phase
  * The variable assignment is processed during the execution phase
  * Variable and function declarations get processed before any code is executed

```javascript
var a;
a = 2;
console.log(a);
```

```javascript
var a;
console.log(a);
a = 2;
```

* Hoisting is done at the scope level
* While function declarations are hoisted, function expressions are not

```javascript
foo(); // TypeError (not ReferenceError)

var foo = function bar() {
  // some stuff
}
```

* `foo` gets hoisted, however `foo` is `undefined` so when it's called as a `function` a `TypeError` is thrown and *not* a `ReferenceError`
* Note that `bar`, though a named function declaration, is not available in the enclosing scope
* Function declarations are hoisted above variable declarations

```javascript
foo(); // 1

var foo;

function foo() {
  console.log(1);
}

foo = function() {
  console.log(2);
}

// How the engine interprets it
function foo() {
  console.log(1);
}

foo();

foo = function() {
  console.log(2);
}
```

* Subsequent variable declarations are ignored
* Subsequent function declarations are overridden

```javascript
foo(); // 2

function foo() {
  console.log(1);
}

function foo() {
  console.log(2);
}
```
