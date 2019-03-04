# [How `Array.prototype.map` works](https://medium.freecodecamp.org/how-array-prototype-map-works-b6b69379c3af)

* This is the polyfill from MDN

```javascript
/*Array.prototype.map implementation*/
Array.prototype.map = function (callback/*, thisArg*/) {
    var T, A, k;
    if (this == null) {
        throw new TypeError('this is null or not defined');
    }
    var O = Object(this);
    var len = O.length >>> 0;
    if (typeof callback !== 'function') {
        throw new TypeError(callback + ' is not a function');
    }
    if (arguments.length > 1) {
        T = arguments[1];
    }
    A = new Array(len);
    k = 0;
    while (k < len) {
        var kValue, mappedValue;
        if (k in O) {
            kValue = O[k];
            mappedValue = callback.call(T, kValue, k, O);
            A[k] = mappedValue;
        }
        k++;
    }
    return A;
};
```

* `O.length >>> 0;` is to handle the case where the `this` context inside the `map` is overridden using `call` / `bind`/ `apply`
  * If the context object is empty, then it's `length` would be `undefined` - by zero shifting, this leads to coercion of non-integer values to integers
  * So `undefined` => `0`
* `arguments.length > 1` check exists because there is optional second `thisArg` that will be used to provide context
  * If second argument exists, then use it as the context when iterating through values and executing the callback
* The reason for having the `k` value is that it represents the index of the array value - this is useful for a map function that is index dependent
