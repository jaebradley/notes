# [JavaScript Function Chaining](https://medium.com/better-programming/javascript-function-chaining-8b2fbef76f7f()

* Function chaining is pattern where methods are called on the same object (i.e. object reference)
  * Example is `jQuery` (`someDiv.css('color: red').height('100px').width('100px');`
  * `jQuery` functions return object reference of element on which previous method was executed

## Example adding / multipliying numbers

```javascript
const result = {
  value: 0,
  addNumber: function(a, b) {
    this.value = a + b;
    return this;
  },
  multiplyNumber: function(a) {
    this.value = this.value * a;
    return this;
  }
}

// This should result in 300
result.addNumber(10, 20).multiplyNumber(10);
```

* `addNumber` operators on the `result` object, so `this` refers to the `result` object
* Same pattern for `multiplyNumber` where it's operating on the returned `result` object (from `addNumber`
