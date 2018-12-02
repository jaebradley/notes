# [Understanding memoization in JavaScript to Improve Performance](https://blog.bitsrc.io/understanding-memoization-in-javascript-to-improve-performance-2763ab107092)

```javascript
function longOperation(args) {
  if (args in cache) {
    // get value from cache
  } else {
    // do operation and store value in cache
  }

  return cachedValue;
}

// Take advantage of cache property to store previous results
function memoize(fn) {
  const args = Array.prototype.slice.call(arguments)
  fn.cache = fn.cache || {};
  return fn.cache[args] ? fn.cache[args] : fn.cache[args] = fn.apply(this, args);
}
```

* Memoization should be used for pure functions where inputs will always lead to predictable outputs
* Memoization trades space for speed so it should be used where there is a limited input range
