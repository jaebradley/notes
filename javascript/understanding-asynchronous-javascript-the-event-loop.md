# [Understanding Asynchronous JavaScript: The Event Loop](https://blog.bitsrc.io/understanding-asynchronous-javascript-the-event-loop-74cd408419ff)

* The JavaScript engine can only process one statement at a time in a single thread

```javascript
const second = () => {
  console.log('Hello there');
}

const first = () => {
  console.log('Hi there');
  second();
  console.log('The End');
}

first();
```

* Technically, some global execution context (i.e. some global function that starts execution) is pushed to top of stack
* `first`
* Then `console.log('Hi there')` is put on top
* This `console` statement is evaluated and popped off leaving `first`
* Then `second` is put on stack
* Then `console.log('Hello there')` is put on stack
* This `console` statement is evaluated and popped off
* `second` has finished executing so it is also popped off
* Then `console.log('The End')` is put on top
* This `console` statement is also evaluated and popped off
* Then `first` is also popped off
* Then this global function will get popped off stack

* The event loop, web APIs, message queue/task queue are not part of JavaScript engine but are part of browser runtime environment or Node runtime environment

```javascript
const networkRequest = () => {
  setTimeout(() => {
    console.log('Some network request');
  }, 2000);
}

console.log('Hello world');

networkRequest();

console.log('The end');
```

* `console.log('Hello world')` is pushed to stack
* `console` statement is executed and pushed off stack
* `networkRequest` is pushed to stack
* `setTimeout` is pushed to stack
* `setTimeout` is executed
  * When it's being executed it starts a timer in the web API environment
* `setTimeout` is popped off stack
* `console.log('The end')` is pushed to stack
* `console` statement is executed and pushed off stack
* When the timer from the `setTimeout` expires the `console.log` callback is pushed to the message queue (but not executed immediately)
  * Instead, the event loop's job is to check the call stack and see if it's empty
  * If it is empty, then the event loop goes to the message queue and pushes the first available message to call stack
  * In this case, the `console` statement is pushed to call stack, evaluated, and then popped off

* `DOM` events also use the message queue for pushing callbacks
* Job / micro-task queue has higher priority than message queue (Promise jobs will get executed before callbacks in message queue)

```javascript
console.log('Start');

const triggerEventLoop = () => {
  setTimeout(() => {
    console.log('Event loop');
  });
}

new Promise((resolve, reject) => {
  resolve('Micro-task');
}).then(message => console.log(message));

console.log('End');

// output should be
//
// Start
// End
// Micro-task
// Event loop
```
