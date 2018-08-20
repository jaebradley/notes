# [Call Site](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md#call-site)

* `this` binding depends on where the function containing `this` (i.e. the "call site") is called and **not where the function itself is declared**
* There is also the "call stack" which is the stack of functions that had to occur in order for a certain piece of code to have been executed
* The "call site" is **in the invocation before the currently executing function**

```javascript
function baz() {
  // call stack: [baz]
  // call site is IN global scope

  console.log('baz');
  bar();
}

function bar() {
  // call stack: [baz, bar]
  // call site is IN baz

  console.log('bar');
  foo();
}

function foo() {
  // call stack: [baz, bar, foo]
  // call site is IN bar

  console.log('foo');
}

baz();
```
