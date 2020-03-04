# [Be The Master Of The Event Loop In JavaScript](https://medium.com/better-programming/be-the-master-of-the-event-loop-in-javascript-part-1-6804cdf6608f)

* The macrotask queue works the same as the main task queue
  * Macrotask queue is for asynchronous statements like `setTimeout` while the main task queue is for synchronous statements
* `requestAnimationFrame`s are executed before page is rendered by the JavaScript engine and `setTimeout`s are executed after the rendering process
  * `rAF`s also go into the macrotask queue (shared with `setTimeout`) and is executed earlier than `setTimeout`
* `Promise`s are put into the microtask queue
  * The microtask queue is only executed once the main task queue is empty
  * Microtask queue has higher priority than macrotask queue
  
```javascript
console.log(1);
Promise.resolve().then(() => console.log(2));
setTimeout(() => console.log(3));
console.log(4);
```

* This will print `1`, `4`, `2`, `3`
* The `Promise` is pushed into the microtask queue, which waits for all tasks are dequeued from the main task queue
* The `setTimeout` sends the tasks to the macrotask queue in `100ms`
* Only after all the queued microtasks are executed will all the queued macrotasks be executed