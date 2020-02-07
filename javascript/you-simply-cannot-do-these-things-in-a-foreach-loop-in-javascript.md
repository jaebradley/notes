# [You Simply Cannot Do These Things in a forEach Loop in JavaScript](https://medium.com/better-programming/you-simply-cannot-do-these-things-in-a-foreach-loop-in-javascript-cd6368264f53)

* `return`ing in a `forEach` callback function will not early exit
  * There is no way to stop or break a `forEach` loop other than by throwing an exception - if you need such behavior, the `forEach` method is the wrong tool
* `break`ing inside a `forEach` loop will throw a `SyntaxError: Illegal break statement`
  * This is because technically, the `break` is not inside a loop
  * Same thing with `continue` statements - a `SyntaxError` will be thrown because the `continue` is not technically inside a loop
* Cannot async / await inside a `forEach` - need to wait for `Promise` to resolve before executing next lines

```javascript
// Translate this...
values.forEach(async (value) => await ...);
console.log('done');

// to this...
async () => {
  await values.forEach(async (value) => await ...);
  console.log('done');
}

```
