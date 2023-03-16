# [Event Loop](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch1.md#event-loop)

* The JS Engine runs inside a hosting environment (often the web browser, but now server via`NodeJS`)
* When JS function makes ajax request JS engine tells hosting environment "hey I'm suspending execution for now, but when network request finishes, call this function back"
* When hosting environment finishes network request, it schedules execution of the callback by inserting it into the event loop

```javascript
// Event loop pseudo-code
var eventLoop = [];
var event;

// Continuously check the event loop
while (true) {
  // If an event exists in the event loop
  // Each time we get here - it's called a "tick"
  if (eventLoop.length > 0) {
    // Get next event in queue
    event = eventLoop.shift();

    try {
      // Try and execute it
      event();
    } catch (e) {
      // Handle any errors
    }
  }
}
```

* `setTimeout` does not put callback on event loop queue
  * `setTimeout` sets up a timer that will put the specified callback function on the event loop for future execution
  * If there are 10 events already on the event loop when the timer puts the callback on the event loop, it's going to have to wait for those 10 events to complete before executing
