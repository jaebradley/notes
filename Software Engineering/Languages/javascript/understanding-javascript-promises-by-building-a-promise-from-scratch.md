# [Understanding JavaScript Promises by Building a Promise from Scratch](https://levelup.gitconnected.com/understand-javascript-promises-by-building-a-promise-from-scratch-84c0fd855720)

* A Promise is just a plain old JavaScript object that will execute code in order (synchronously) even if there is a delay
* Implementing a Promise
  * Constructor takes callback function that takes `resolve` function and `reject` function
  * Internal array of functions when Promise resolves
  * When `then` is called with callback, the callback is added to array of functions
  * When first Promise resolves, then each function in Promise chain (internal array) is called using previous value to calculate next value

```javascript
class PromiseSimple {
  constructor(executionFunction) {
    this.promiseChain = [];
    this.handleError = () => {};

    this.onResolve = this.onResolve.bind(this);
    this.onReject = this.onReject.bind(this);

    executionFunction(this.onResolve, this.onReject);
  }

  then(onResolve) {
    this.promiseChain.push(onResolve);

    return this;
  }

  catch(handleError) {
    this.handleError = handleError;

    return this;
  }

  onResolve(value) {
    let storedValue = value;

    try {
      this.promiseChain.forEach((nextFunction) => {
         storedValue = nextFunction(storedValue);
      });
    } catch (error) {
      this.promiseChain = [];

      this.onReject(error);
    }
  }

  onReject(error) {
    this.handleError(error);
  }
}
```
