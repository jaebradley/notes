# [Mastering JS console.log like a Pro](https://medium.com/javascript-in-plain-english/mastering-js-console-log-like-a-pro-1c634e6393f9)

* `%o` is a placeholder for an object, `%s` is a placeholder for a string, `%d` is a placeholder for a number
  * Pass in placeholder values as arguments to `console.x` method after first argument, which represents the actual message
  * Use the `%c` placeholder to add CSS to console message - `console.log('%cFoobar', 'color: red; font-size: large');
* `console.dir` is used to print a JSON representation of an object
* `console.table` for a more straight-forward way of representing an object, especially an array of objects
* `console.group` and `console.groupEnd` to group / nest console message
* `console.trace` to print stacktrace of code execution
* `console.time` and `console.timeEnd` to track timing of various operations
* `console.memory` to inspect various memory data information about browser
