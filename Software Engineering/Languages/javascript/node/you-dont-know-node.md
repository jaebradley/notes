# [You Don't Know Node](https://medium.com/edge-coders/you-dont-know-node-6515a658a1ed)

* What is the call stack and is it part of V8?
  * It's the data structure that V8 uses to keep track of function invocations
  * Every time a function is invoked, V8 puts a reference to that function on the call stack and keeps doing so for each nested invocation of any other functions
  * When nested function invocations resolve, V8 pops them off the call stack and uses the returned value in their place
  * Only one call stack per node process, so if you keep the call stack busy, the node process is busy
* What is the Event Loop? Is it part of V8?
  * Event loop is provided by `libuv` library and is not part of V8
  * Event loop is the entity that handles external events and converts them to callback invocations
  * It is a loop that picks events from the event queues and pushes their callbacks onto the call stack
  * Node API introduces functions like `setTimeout` and `fs.readFile` - these are not part of JavaScript itself
* What will node do when call stack and event loop queues are all empty?
  * It exits
  * When a node program starts, node automatically starts event loop and when the event loop is idle, the process exits
  * To keep a node process running, need to place something somewhere in event queues
    * When starting an HTTP server, basically telling event loop to keep running and checking on those events
* What is the difference between `module.exports` and `exports`?
  * `exports` is a reference to `module.exports`
  * Only way _not_ to export API of module is to do `exports = ...` - you just get local variable in module scope
* How come top-level variables are not global?
  * You have `module1.js` that defines a top-level variables
  * If there is `module2.js` that requires `module1` and try to access top-level variable in `module1`, get `ReferenceError`
  * This is because node files get wrapped in their own IIFE and thus all variables declared in a node file are scoped to that IIFE / file

```javascript
// script.js

console.log(arguments);

// In node this will output arguments because 
// node executed a function (the IIFE)
// with 5 arguments
```

* The objects `exports`, `require`, and `module` are all globally available in every file but they are different in every file. How?
  * `require` can be used as a "global" variable but `require` in different files points to two different objects
    * This is because `require` (as well as the other objects) are passed in as arguments to the IIFE that wraps every single node file
    * So they appear to be "global" but are actually just function arguments