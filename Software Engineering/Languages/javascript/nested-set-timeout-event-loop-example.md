# [Nested setTimeout and the Event Loop](https://stackoverflow.com/a/51599327/5225575)

```javascript
function callback() {
  setTimeout(function inner() {
    console.log('hello inner!');
  }, 0);
  console.log('hello callback!');
}
```

* The behavior of the above is
  * `hello callback!`
  * `hello callback!`
  * `hello inner!`
  * `hello inner!`
* Think of execution of file as first thing on event loop
  * Only when it's finished can another event loop function be invoked
* So after execution of file, event loop queue looks like
  * `callback`
  * `callback`
* Then first `callback` is plucked off of the queue and is executed
  * `callback` invokes another `setTimeout`
  * `inner` is appended to the event loop
  * then `console.log` is invoked
* Now event loop queue looks like
  * `callback`
  * `inner`
* Second `callback` is plucked off of the queue and executed
  * Same thing happens as before (another `inner` gets added to event loop)
* And then both `inner`s are executed

