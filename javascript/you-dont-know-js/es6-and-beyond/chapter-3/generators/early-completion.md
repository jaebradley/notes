# [Early Completion](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch3.md#early-completion)

## `return`

* `return` method forces end of execution at that moment where you get specified value right back
* `return` also gets called at end of iterator execution in `for...of` or by spread operator
* The reason for the `return` mechanism is to provide a way to clean up tasks by freeing resources, for example
* `return` triggers the `finally` clause (if it exists) to be executed

```javascript
function* foo() {
  try {
    yield 1;
    yield 2;
    yield 3;
  } finally {
    console.log('cleanup');
  }
}

// 1
// 2
// 3
// 'cleanup'
for (var result of foo()) {
  console.log(result);
}

var iterator = foo();

iterator.next(); // { value: 1, done: false }

// 'cleanup'
// { value: 42, done: true }
iterator.return(42);
```

## `throw`

* `throw` basically injects a `throw` statement at the pause point
* If a `finally` block exists, the `finally` block would execute before the `throw` is propagated to the calling code
* Nested `try` / `catch`es - `throw` propagates a level at a time (if no `try` / `catch` then would propagate further and further up)

```javascript
function* foo() {
  try {
    yield 1;
  } catch (e) {
    console.log(e);
  }

  // After unpausing here, continues execution until next yield
  // (which, in this case, doesn't exist so will effectively get caught in bar)
  // (and then continue to bar's throw)
  // (which will then get caught by the "global" try / catch)
  yield 2;

  throw 'Hello!';
}

function* bar() {
  try {
    yield* foo();
  } catch (e) {
    console.log(e);
  }

  throw 'Goodbye!';
}

var iterator = bar();

iterator.next(); // { value: 1, done: false }

// Error: foobar
// { value: 2, done: false }
iterator.throw('foobar');

try {
  // Hello!
  iterator.next();
} catch (e) {
  // Goodbye!
  console.log(e);
}

iterator.next(); // { value: undefined, done: true }
```
