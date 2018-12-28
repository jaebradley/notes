# [Iterator Control](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch3.md#iterator-control)

* `yield`s allow passing values back into the execution flow via `next`

```javascript
function* foo() {
  var x = yield 1;
  var y = yield 2;
  var z = yield 3;
  console.log(x, y, z);
}

var iterator = foo();
iterator.next(); // { value: 1, done: false }

// 'foo' is assigned to x
iterator.next('foo'); // { value: 2, done: false }

// 'bar' is assigned to y
iterator.next('bar');

// 'baz' is assigned to z
iterator.next('baz');

iterator.next(); // 'foo', 'bar', 'baz'
```
