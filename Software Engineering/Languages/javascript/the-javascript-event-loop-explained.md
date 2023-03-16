# [The JavaScript Event Loop: Explained](https://blog.carbonfive.com/2013/10/27/the-javascript-event-loop-explained/)

## I/O

* Almost all I/O is non-blocking (HTTP requests, database operations, disk reads / writes)
* Single thread of execution asks the JavaScript runtime to perform an operation, providing a callback function, and then moves on to do something else
* When the operation has been completed, message is enqueued along with provided callback function
* In future, message is dequeued and callback is fired

## Event Loop

* Due to JavaScript being single-threaded, further message polling and processing is halted until all calls on call stack return
* Subsequent synchronous function calls add new call frames to the stack
* Queue is polled for the next message - this is called a `tick`
* If two asynchronous functions are called in the same call frame, the callbacks would be queued in the order of invocation (two `setTimeout`s with `0` as the timeout value, for example)

## On Click Handler Example

```javascript
function() changeColor {
  console.log('this would change color');
}

function() changeColorOnClick {
  var link = document.querySelector('#some-link');
  link.addEventListeners('click', changeColor);
}

changeColorOnClick();
```

* Dequeue first message, which is `global eval: changeColorOnClick`
* `changeColorOnClick` executes `addEventListener` which adds the `changeColor` on `link` `click` callback - this means that when someone clicks the link, a message is enqueued on the message queue with a message and the callback, `changeColor`
* When the message is dequeued, `changeColor` is executed in the call stack - as noted previously, any additional message polling and processing is halted pending the return of all calls on the stack

## Web Worker

* Offload expensive operations on a separate thread of execution, freeing up main thread
* Web worker has a separate message queue, event loop and memory space
* Communication between web worker and main thread is done via message passing
