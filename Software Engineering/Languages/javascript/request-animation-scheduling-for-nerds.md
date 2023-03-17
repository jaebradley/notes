# [Request Animation Scheduling for Nerds](https://medium.com/@paul_irish/requestanimationframe-scheduling-for-nerds-9c57f7438ef4)

* Any request animation frames queued in event handlers will be scheduled in the same frame
* Any request animation frames queued in a request animation frame will be executed in the next frame
* Request animation frames are not throttled
  * If there are five callbacks queued up and they each take 100ms to run the browser will _not_ distribute them across multiple frames
  * It will try to run all callbacks in the intended frame which can cause significant performances problems
  * Performance problems can happen when you request callback at end of request animation frame or when request animation frame callbacks that are associated with input handlers
* Timestamp that's part of request animation frame callback represents the beginning of main frame of current thread
* Same timestamp can be outputted multiple times if callback is executed within the same frame like

```javascript
const outputTimestamp = _ => requestAnimationFrame(ts => console.log("Inside request animation frame ${ts}");

// Output timestamp every 2ms for 200ms
const id = setTimetout(outputTimestamp, 2);
setInterval(_ => clearTimeout(id), 200);
```
