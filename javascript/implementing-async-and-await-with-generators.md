# [Implementing Async And Await With Generators](https://medium.freecodecamp.org/how-to-implement-async-and-await-with-generators-11ab0859010f)

* `async`/`await` seems to wait for promise to return a value and proceed execution
* Seem like something a function wrapping a generator could do by
  * Looking for yielded values
  * These yielded values would be in `Promise`s
  * Wait for `Promise` to resolve
  * Continue execution
* This wrapping function would consume each `yield`
* The yielded value would be a `Promise`
* When the `Promise` is settled, we take the result and continue executing the generating using the settled value, if necessary
* If `Promise` is rejected, we need a `catch` handler to throw exception by calling `generator.throw`

```javascript
function asynq(generator) {
  const generatorObject = generator();

  return (function resolve(result) {
    if (result.done) {
      return Promise.resolve(result.value);
    }

    return Promise.resolve(result.value)
      .then(value=> resolve(generatorObject.next(value)))
      .catch(error => resolve(generatorObject.throw(error)))
  }(generatorObject.next()));
}
```

* Given the generator function, create a Generator object
* Then invoke a function (in this case, called `resolve`) with the first `yield`ed value from the generator
* In the execution of this function, the object returned by the iterator is checked to see if the iterator is done
  * If the iterator is done, return a resolved `Promise` with the iterator object value
  * If the iterator is not done, return a resolved `Promise` with the `resolve` function being called recursively with the _next_ `yield`ed value from the Generator object iterator using the previous value
  * If an error occurs, call the `throw` method on the iterator

