# [Event Loop Timers and `nextTick`](https://nodejs.org/en/docs/guides/event-loop-timers-and-nexttick/)

* There are various phases of the event loop
  * Timers - executes callbacks scheduled by `setTimeout` and `setInterval`
  * Pending callback - executes I/O callbacks deferred to next loop iteration
  * Idle, prepare - only used internally
  * Poll - executes I/O callbacks
    * Does not execute close callbacks, ones scheduled by timers, and `setImmediate`
    * Node may block here
  * Check - executes `setImmediate`
  * Close callbacks - some close callbacks like `socket.on('close', ...)`
* Each phase of the event loop has a queue of callbacks to process
* Generally, once this queue is empty (or max number of callbacks is reached), the event loop will proceed to the next phase

## Timers

* Callbacks defined via `setTimeout` or `setInterval` determine the minimum amount of time that a scheduled callback could be executed
* Operating system scheduling or other callbacks could change the time it takes for these callbacks to get executed

## Poll

* Poll phase does two things
  * Figure out how long to block and poll for I/O events
  * Process I/O events in poll queue
* When poll phase is entered
  * If no timers are scheduled
    * If poll queue is empty
      * If `setImmediate`s callbacks have been registered, move to `check` phase and execute callbacks
      * If no `setImmediate` callbacks have been registered, wait for callbacks to enter queue and then execute them immediately
    * If poll queue is not empty
      * Execute callbacks in queue synchronously (until queue is empty or limit is reached)
  * When poll queue is empty, check if any expired timers exist and then go back to timers phase

## Check

* Executes any `setImmediate` callbacks
  * `setImmediate` is a special timer that runs in a special part of event loop
  * `setImmediate` callbacks get executed after poll phase
* When `setImmediate` callback is scheduled, if poll phase becomes idle, it will move to check phase and execute scheduled callbacks

## `setImmediate` vs. `setTimeout`

* `setTimeout` is supposed to execute a callback after a minimum amount of time
* `setImmediate` is supposed to execute a callback after completion of the current poll phase
* When `setTimeout` and `setImmediate` are executed in the main context, the execution order is non-deterministic
* When they are executed in an I/O context like the callback for `fs.readFile`, `setImmediate` will always execute before `setTimeout`
  * I'm not 100% sure why this happens, but I'm guessing it's because the poll queue is considered to be idle when the `readFile` callback is executing and the `setImmediate` callback has been scheduled?

## Additional article

* Excellent [SO post that walks step-by-step through event loop](https://stackoverflow.com/a/47727402/10039741)
