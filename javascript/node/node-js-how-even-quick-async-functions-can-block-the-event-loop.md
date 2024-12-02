# [Node.js: How even quick async functions can block the Event-Loop, starve I/O](https://medium.com/@michael.gokhman/nodejs-how-even-quick-async-functions-can-block-the-event-loop-starve-io-8c416d76daa3)

* Situation is an `async` / `await` calculation in a `for` loop that blocks event loop
  * However, replacing `async` / `await` with a `setTimeout` of `0` does not block event loop but does increase performance penalty
* When a callback enqueues another callback that can be handled in the same phase, then it will be handled before moving to the next phase
* Only in the `poll` phase (after `timers`, `pending callbacks` phases) does Node check the network socket for new requests
* `setTimeout` helped because every time a timer callback was enqueued, the event-loop has to loop through all the phases to reach the `timers` phase, passing through the `poll` phase
* In the `await` / `async` case, every loop iteration creates a `Promise` and when it's resolved, the loop continues to the next iteration
* The `next tick queue` processes callbacks registered via `nextTick` and the `micro tasks queue` handles resolved promises
  * If a resolved `Promise` creates another `Promise`, it is handled in the same phase before moving to the next one
* Callbacks passed to `process.nextTick` will ve resolved before the event loop continues
* This can create some bad situations because it allows you to "starve" your I/O by making recursive `process.nextTick` calls
