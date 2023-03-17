# [Temporal Dead Zone](https://github.com/getify/You-Dont-Know-JS/blob/master/types%20%26%20grammar/ch5.md#using-variables-too-early)

* `ES6` introduced a new concept of `Temporal Dead Zone`
* It's when a variable reference cannot be made because it hasn't reached it's required initialization
  * Classic case is assigning a value to a variable declared with a `let` before the `let` declaration occurs
  * Unlike `var` declarations, `let`s and `const`s are not hoisted
* For undeclared variables, the `typeof` operator will return `undefined`
* For `TDZ` variables, the `typeof` operator will throw a `ReferenceError`

```javascript
// This will throw a ReferenceError

{
  a = 42;
  let a;
}
```

```javascript
// This will throw a ReferenceError

{
  // This will return undefined
  typeof b;
  // This will throw a ReferenceError
  typeof a;
  let a;
}
```

* Another place where `TDZ` can occur is defining default function argument values by using parameters that have not been declared yet

```javascript
// Will throw ReferenceError since b is not defined yet
// a is fine since it's been declared before b is evaluated
function foo(a = 1, b = b + a + 5) {
  // won't get here
}
```
