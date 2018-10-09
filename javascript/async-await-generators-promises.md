# [`Async-Await = Generators + Promises`](https://hackernoon.com/async-await-generators-promises-51f1a6ceede2)

* Imagine an `async` function like

```javascript
async function init() {
  const firstResult = await someAsyncTask1();
  console.log(firstResult);

  const secondResult = await someAsyncTask2(firstResult);
  console.log(secondResult);

  const thirdResult = await someAsyncTask3(secondResult);
  console.log(thirdResult);

  return thirdResult;
}
```

* Each asynchronous task depends on the output of the completion of the previous asynchronous task

* Generator functions are executed `yield`-by-`yield` (i.e. one `yield` expression at a time, by it's `iterator`)
  * This `iterator` is created when the generator is invoked
  * This `iterator` iterates from `yield` to `yield` via the `next` method
  * Every `yield` has a `give -> halt -> take` behavior
    * It `gives` "out" the value of the `yield` expression to the `iterator`
    * It `halts` until the `next` method is called
    * It `takes` the value passed with the `next` method and replaces the halted expression with that value
    * It then moves on to the `next` (heh) `yield`
* Generators can `yield` `Promise`s
  * Can imagine that the `iterator` can be controlled to halt until `Promise` resolves in some fashion

* Remember, our previous `async` function? We can maybe rewrite it in a generator like

```javascript
function* init() {
  const firstResult = yield someAsyncTask1();
  console.log(firstResult);

  const secondResult = yield someAsyncTask2(firstResult);
  console.log(secondResult);

  const thirdResult = yield someAsyncTask3(secondResult);
  console.log(thirdResult);

  return thirdResult;
}
```

* Need a way to actually halt the `iterator` until `Promise`s get resolved

```javascript
// runner takes a generator function
// executes generator function to completion
function runner(generator) {
  // instantiate iterator for generator function
  const iterator = generator();

  // create function to iterate through generator function
  // the run function is called recursively when a Promise is resolved
  // and, as noted above, will continue calling iterator.next until generator is completed
  function run(value) {
    const result = iterator.next(value);
    if (result.done) {
      return result.value;
    }

    return Promise.resolve(result.value).then(run);
  }

  return run();
}
```

* So previous generator function that `yield`ed `Promise`s can be executed like `runner(init)`
