# [Promises](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch4.md#promises)

* Promises are not about eliminating callbacks - they provide a trusty intermediary for handling callbacks
* Can think about `Promise`s as an event listener (albeit a one-time event listener) that let's you know when task is completed
* Can also think about `Promise`s as a future value where the value can be reasoned about independent of time
* Promises are either fulfilled with a value or rejected with a message
* After being fulfilled or rejection, they are immutable - i.e they cannot be subsequently fulfilled or rejected
* `Promise` constructor takes a single function that takes two function arguments
  * First function is called on fulfillment and is passed a value
    * When this function is called without a value or with a non-`Promise` value, the `Promise` is fulfilled
    * When this function is called with a `Promise`, the `Promise` adopts the state of the passed `Promise`
  * Second function is called on rejection and the value passed to the function is set as the rejection reason
* `Promise`s have a `then` method that takes one or two callbacks
  * First callback, if it exists, gets called if `Promise` is fulfilled successfully
    * If omitted / not a valid function, default is to pass fulfilled value along
  * Second callback, if it exists, gets called if `Promise` is rejected or if an exception is thrown during resolution
    * If omitted / not a valid function, default is to propagate rejected reason along
    * `catch(handleRejection)` and `then(null, handleRejection)` are equivalents
* `Promise.resolve` can accept a `Promise` and adopt it's state but `Promise.reject` will adopt `Promise` as rejection reason and not `Promise`'s underlying value

```javascript
// Rewrite callback to use Promises

// Callback approach
function ajax(url, cb) {
  // make request
  // call callback function
}

ajax(url, function(err, response) {
  if (err) {
    // handle err
  } else {
    // handle response
  }
});

// Promise-based approach
function ajax(url) {
  return new Promise(function(resolve, reject) {
    // make request
    // call resolve or reject at some point
  });
}

ajax('foobar.com')
  .then(
    function fulfilled(response){
      // do something with response
    },
    function rejected(error) {
      // do something with error
    }
  )
```
