# [Understanding Scope](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch1.md#understanding-scope)

* `Engine` - responsible for compilation and execution of JavaScript code
* `Compiler` - responsible for parsing and code-generation
* `Scope` - responsible for maintaining list of variables and rules that dictate if certain variables are accessible to executing code

## Processing `var a = 2;`

* `Compiler` sees `a`
* `Compiler` asks `Scope` if a variable called `a` exists in the current scope
  * If variable exists, `Compiler` ignores declaration and moves on
  * Otherwise, `Compiler` asks `Scope` to declare a new variable `a` for current scope
* `Compiler` generates code for `Engine` to execute
* `Engine` runs code that will
  * Ask `Scope` if a variable called `a` exists in current scope
  * If variable exists, use it
  * Else, `Engine` **looks through nested scopes** to find variable
  * If `Engine` finds the variable, it assigns `2` to it
  * If `Engine` doesn't find variable, it throws an error

## Types of Variable Lookups

* `RHS` variable lookup
  * Retrieve value for a particular variable
  * `console.log(a)` - reference to `a` is a `RHS` since we're only retrieving and not assigning
* `LHS` variable lookup
  * Find variable and set as target for variable assignment
  * `a = 2;`

```javascript
function foo(a) { // implicit LHS lookup when assigning 2 to a
  console.log(a); // RHS reference for a, and console object
}

foo(2); // RHS reference for foo
```

## [Nested Scope](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch1.md#nested-scope)

* If a variable cannot be found in the immediate scope, `Engine` looks at the containing scope, and it's continuing scope (etc.) until it finds the variable or reaches the global scope

```javascript
function foo(a) {
  console.log(a + b); // b is not in this scope, but it is in global scope
}

var b = 2;

foo(2); // 4
```

## Why does it matter if something is a `RHS` lookup or a `LHS` lookup

* Lookups behave differently when variable has not been declared

```javascript
function foo(a) {
  console.log(a + b); // b is not found, and will throw a ReferenceError
  b = a; // If previous line was commented out, a b variable would've been created in the global scope
}

foo(2);
```

* When a `RHS` lookup fails to find a variable (in any of the nested scopes), a `ReferenceError` is thrown
* When a `LHS` lookup fails to find a variable, **a variable with that name will be created in the global scope**
  * This only happens when `Strict Mode` is turned off
  * If a `LHS` lookup failed with `Strict Mode` turned on, a `ReferenceError` would also be thrown
