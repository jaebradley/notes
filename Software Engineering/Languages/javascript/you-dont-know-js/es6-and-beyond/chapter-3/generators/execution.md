# [Generators](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch3.md#generators)

* Generators return an iterator that is used to control execution
* `yield`s signal a pause point
  * It also sends out a value when the generator is paused
  * It also is replaced by the eventual resumption value

```javascript
function* foo() {
  var x = yield 10;
  console.log(x);
}
```

* `foo` will `yield` out `10` when paused
* When resuming the generator, with `.next()`, the value passed into `next` will be assigned to `x`
* `yield` can validly appear anywhere variable assignment can appear

```javascript
yield 2 + 3; // same as yield (2 + 3)
(yield 2) + 3; // yield 2 first, then + 3;
```

## `yield *` - `yield` delegation

* `yield *` requires an iterable - it will then invoke that iterable's iterator and delegate generator control to that iterator until it's exhausted
* `yield *` will also capture `return` values
  * While built-in iterators don't have return values, custom iterators might
  * `return` values are the completion value of a generator - so they could become assigned in a double-delegation case

```javascript
function* foo() {
  yield 1;
  yield 2;
  yield 3;
  yield 4;
  return 5;
}

function* bar() {
  yield* foo(); // 1, 2, 3, 4, 5
}

function* baz() {
  var x = yield* foo();
  console.log('X is: ', x);
}

for (var value of baz()) {
  // Will console.log
  // 1, 2, 3, 4
  // X is: 5
  // This is because 1, 2, 3, 4 are yielded out of foo
  // Which are then yielded out of baz
  // Which are then console logged
  // 5 is returned by foo and assigned to the variable x
  // x is console logged
  console.log(value);
}
```
