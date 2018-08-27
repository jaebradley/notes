# [Error Handling](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch3.md#error-handling)

* `try...catch` is synchronous only (so it doesn't work across async operations)

```javascript
var p = Promise.resolve(42);

p.then(
  function fulfilled(msg) {
    console.log(msg.toLowerCase());
  }
).catch(handleErrors);
```

* What happens if `handleErrors` throws? Who catches it's error?
* A common suggestion is that every `Promise` should have a `.done` which will throw a global error if it receives a `Promise` that is rejected
  * However, `.done` is not part of the `ES6` standard
