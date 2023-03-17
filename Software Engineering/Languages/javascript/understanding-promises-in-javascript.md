# [Understanding Promises in JavaScript](https://hackernoon.com/understanding-promises-in-javascript-13d99df067c1)

* Promises are created by taking a callback in the constructor
* This callback takes two arguments, a `resolve` argument and a `reject` argument
* Both `resolve` and `reject` represent functions that will be executed when the asynchronous code that is run in the defined function reaches different states
* The `resolve` callback is called when some asynchronous code has successfully finished executing (like an API call)
* The `reject` callback is called when some asynchronous code has unsuccessfully finished executing (exception is thrown)

```javascript
new Promise(function(resolve, reject) {
  setTimeout(function() {
    resolve({
      message: "The man likes to keep his word",
      code: "aManKeepsHisWord"
    });
  }, 10 * 1000);
});
```

* When Promises are created they have a status of "pending" (and "resolved" and "rejected" when the `resolve` or `reject` callbacks are called)
* Resolved or rejected Promises are said to be "settled"
* When a Promise is resolved, it's `then` method will be called with the value passed to the `resolve` function
  * `then`'s first callback is an `onFulfilled` and it's second callback is `onRejected`
* When a Promise is rejected, it's `reject` method will be called with the value passed to the `reject` function
* `finally` method will always be called regardless of if Promise was fulfilled or rejected
* `catch`, `then`, and `finally` return `Promise`s
