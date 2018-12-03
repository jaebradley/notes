# [Iteration](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#iteration)

* The order of iteration over an object's properties is *not guaranteed* and may vary between different JS engines
* The order of iteration across object's properties should not be relied upon to be consistent
* `for..of` loop asks for an iterator object (from default internal function called `@@iterator`) from thing to be iterated (like array)
  * Then loop calls iterator object's `next` method on each loop iteration
  * `@@iterator` internal property is set using `Symbol.iterator`
  * `@@iterator` is not the iterator object itself but a function that returns the iterator object

```javascript
// This will iterate over all enumerable properties on object
Object.defineProperty(someObject, Symbol.iterator, {
  enumerable: false,
  writable: false,
  configurable: true,
  value: function() {
    const o = this;
    const index = 0;
    const keys = Object.keys(o);
    return {
      next: function() {
        return {
          value: o[keys[index++]],
          done: index > keys.length;
        };
      }
    };
  }
});
```
