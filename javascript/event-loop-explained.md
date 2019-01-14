# [JavaScript Event Loop Explained](https://medium.com/front-end-weekly/javascript-event-loop-explained-4cd26af121d4)

* JavaScript (the language) is single-threaded
* Asynchronous behavior is not part of the JavaScript language itself but is built on top of the core JavaScript language in the browser and accessed through browser APIs

```javascript
function main() {
  console.log('A');
  setTimeout(function displayB() {
    console.log('B');
  }, 0);
  console.log('C');
}
```

* The `main` function will output `A` then `C` then `B`
* `main` gets put on the call stack
* `console.log('A')` gets put on the call stack
* `console.log('A')` is executed and popped off call stack
* `setTimeout` is put on call stack
* `setTimeout` is executed and callback uses Browser API to delay callback for `0` ms
  * This callback will be added to the message queue when the timer runs in the browser (so will probably be added immediately)
* `console.log('C')` gets put on call stack
* `console.log('C')` is executed and popped off call stack
* `displayB` is added to message queue
  * **For the browser to push any message from the queue to the call stack, the call stack has to be empty first**
  * **This means that the `displayB` callback has to wait until all frames in the call stack are complete**
* `displayB` is added to call stack and executed
* This also means if some wait time is added via a `while` loop, that will also delay when `displayB` gets added to the call stack since the `while` loop will live in the call stack until it finishes execution since `while` is blocking
