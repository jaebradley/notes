# [Node.js: How even quick async functions can block the Event-Loop, starve I/O](https://medium.com/@michael.gokhman/nodejs-how-even-quick-async-functions-can-block-the-event-loop-starve-io-8c416d76daa3)
* Scenario is two endpoints, one of which is CPU intensive (100k cryptographic computations)
* Requests to this one endpoint will block *all* requests to the server, including the other endpoint as the event-loop scheduler was not given control
* Update by yielding back control to the event loop after every computation step via async/await-ing every cryptographic computation - but this still blocked
* Adding a `setTimeout` instead led to unblocked behavior, but at a performance cost (CPU was ~10%)
* `timers` -> `pending callbacks` -> `idle, prepare` -> `poll` -> `check` -> `close callbacks`
* If a callback triggers another call to `process.nextTick` the event loop will process the next callback in the same loop, breaking only once the `nextTick` queue is empty
* The main advantage of `setImmediate` over `setTimeout` is that `setImmediate` will always be executed before any timers, if scheduled within an I/O cycle, independently of how many timers are present
* If the **poll** phase becomes idle and scripts have been queued with `setImmediate`, the event loop may continue to the **check** phase rather than waiting

## Bonus: Why the `0` in `setTimeout(0)` is not really `0`
* `setTimeout(0)` had made the process 90% idle
* Idle CPU usually implies that `node`’s main thread invoked a system-call that caused it to suspend execution (i.e. sleep)
* Sleeping occurs via the `poll` system call - specifying a timeout of `0` causes `poll` to return immediately
* It looks like a timeout value greater than `0` is being passed to `poll`
* Following stack trace, will see that there’s a `Timeout` constructor that coalesces a timeout value of `0` to `1`
