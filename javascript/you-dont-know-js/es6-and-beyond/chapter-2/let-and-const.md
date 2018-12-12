# [Block-scoped Declarations](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch2.md#block-scoped-declarations)

## `let`

* The fundamental unit of variable scoping has been functions
* Unlike `var` , which attach themselves to the function scope no matter where they are invoked, `let` declarations attach to the block scope, but are not initialized until they appear in the block
* Accessing a `let` before it's declared leads to a `Temporal Dead Zone` error (though probably will surface as a `ReferenceError`)
  * Trying to access a variable that has been declared but not initialized
    * "initialization" does not require that a value has been assigned - `let a;` is equivalent to `let a = undefined;`
    * You have to run `let a` before accessing `a` explicitly
  * Remember, that this won't be the case with `var`s

```javascript
{
  console.log(a);
  console.log(b); // ReferenceError

  var a = 1;
  let b = 2;
}
```

* `let` in a `for` loop will redeclare itself on each iteration of the `for` loop

## `const`

* `const` is a variable declaration for a read-only variable
* `const` does not indicate that it's value is frozen or immutable - it just indicates that variable cannot be reassigned
  * If `const a` references an array, can still push / modify that array
