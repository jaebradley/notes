# [JavaScript Execution In A Nutshell](https://medium.com/swlh/javascript-execution-in-a-nutshell-210f16e841ec)

* JS engines generally implement a heap, call stack, message queue, and event loop and interact with Web APIs like `setTimeout` and `setInterval`
* Event loop constantly tries to move messages from the message queue to the call stack to be processed
  * It does this by polling messages in the queue and pushing them to call stack if call stack is empty
* Two execution contexts - global context when JS engine first starts interpreting code, while function execution context is created whenever a function is invoked
* Execution context has a creation phase where 
  * memory space is allocated for functions / variables
  * function declarations are placed into memory space
  * variable declarations are placed into memory and assigned a default of `undefined`
  * `this` binding is created

```javascript
let num = 3;

function double(number) {
  return number * 2;
}

function quadruple(number) {
  return double(double(number));
}

function quadrupleSlowly(number) {
  setTimeout(() => {
    console.log(quadruple(number));
  }, 1000);
}

quadrupleSlowly(num);
```

* `num` is assigned to memory and set to `undefined` ("creation phase" of global execution context)
* `double`, `quadruple`, and `quadrupleSlowly` are assigned to memory
* `num` is set to `3` ("execution phase" of global execution context)
* `quadrupleSlowly` is pushed onto call stack
  * `setTimeout` is pushed onto the call stack
  * `setTimeout` finishes execution, sends Web API request, stack frame is popped off call stack
  * `quadrupleSlowly` finishes execution and is popped off call stack
* Call stack is empty
* `setTimeout` timer completes and callback is pushed onto message queue
* Event loop notices message queue is populated and call stack is empty
* `setTimeout` callback is pushed onto call stack
  * `console.log`, `quadruple`, `double`, and another `double` all get pushed to call stack and executed