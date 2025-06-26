# [Event Loop Fundamentals](https://webperf.tips/tip/event-loop/)
* Tasks are units of execution that are scheduled on the Main Thread
* Parsing HTML / CSS, user input, compiling/executing JS, receiving network data, rendering
* Anything that directly requires access to the DOM must be run on the Main Thread as the DOM is not thread-safe
* Only one task can run on the Main Thread at a time
* This task must run to completion before another task can execute
* Since it is impossible ro run multiple tasks simultaneously on the Main Thread, any task that is not actively running resides in the task queue data structure
* A completed render task produces a frame
* These render tasks have greater priority than other tasks on the task queue (task queue is not a FIFO)
* Event loop is an infinite loop that checks whether to execute a task on the task queue or to execute a render task
* Event loop waits for selected task to run on main thread until completion - will never pause or block the main thread

## Non-Blocking
* Asynchronous logic (like `setTimeout`) does not block the main thread
* Tells the browser to queue a task to run `some callback` on the task queue after `some delay`

```
let count = 0;

// Queue a Task on the Task Queue every 500ms
setInterval(() => {
    // This code runs in a Task
    count++;
    const node = document.getElementById('output');
    node.innerText = `${count} Timers Fired!`
}, 500);
```

* Callback edits DOM every 500 ms
* 500 ms after executing the `setInterval` function, the callback function is queued on the task queue
* Event loop pulls the task off the task queue and runs it on the main thread
* Task edits the DOM which then creates a render task for the event loop to execute
* Render task produces a frame, which is when the DOM edits are visible
