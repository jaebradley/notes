# [3 Most Common Mistakes In Using `Promises` In JavaScript](https://medium.com/@mpodlasin/3-most-common-mistakes-in-using-promises-in-javascript-575fc31939b6)

## Wrapping `Promises` in a `Promise` constructor

```js
const nextPromise = new Promise(resolve => {
  someOtherPromise.then(result => {
    // some processing of result
    resolve(result);
  });
});
```

* The issue here is that, this is effectively equivalent to `nextPromise = someOtherPromise.then(someProcessingFunction)`
* Difference is around error-handling - need to add a `catch` to the inner `Promise` that links to the `reject` argument of the outer `Promise`
  * This `catch` will handle errors that occur either in `someOtherPromise` or in it's `then` callback
  * In the article, it points out that `then` takes an optional second parameter for handling errors, but these errors are only to handle errors in `someOtherPromise` and not in the `then` callback

```js
const nextPromise = new Promise((resolve, reject) => {
  someOtherPromise.then(result => {
    // some processing of result
    resolve(result);
  }).catch(reject);
});
```

## Executing a `Promise` immediately after creation

* The `Promise`'s callback when constructing a `Promise` gets executed immediately after creation - basically, `Promise`'s are eager
  * So in the following example, the inner `resolve` callback gets executed immediately / the http request will be scheduled immediately

```js
const result = new Promise(resolve => {
  // some HTTP request
  resolve(response);
});
```

* To make the creation of a `Promise` "lazy", create a `Promise` factory by creating a function that returns a `Promise` 

```js
const resultCreator = () => new Promise(someAsyncCallback);
```
