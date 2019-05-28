# [Debouncing and Throttling Explained Through Examples](https://css-tricks.com/debouncing-throttling-explained-examples/)

## Debounce

* Elevator analogy for debounced events
  * Before doors close, person enters elevator
  * Right before doors close again, person enters elevator
  * Each time, the function (i.e. elevator moving floors) is delayed until doors are closed
* Leading edge
  * You might want the function to execute immediately, and then fire again after there is a pause after all the rapid calls
* Handle burst of events as one event (like keystrokes)
* Don't call debounce more than once

```javascript
// Wrong
window.on('scroll', function() { debounce(handleScroll, 300); });

// Right
window.on('scroll', debounce(handleScroll, 300));
```

## Throttle

* Doesn't allow function to execute more than once per time interval
* Throttle guarantees execution of function regularly
* Infinite scrolling example
  * Debounce doesn't help because it would only trigger once user stops scrolling
  * Throttle guarantees that we're always checking distance from bottom

