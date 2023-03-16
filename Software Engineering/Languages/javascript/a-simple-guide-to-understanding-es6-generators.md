# [A Simple Guide to Understanding `ES6` Generators](https://medium.com/dailyjs/a-simple-guide-to-understanding-javascript-es6-generators-d1c350551950)

* `Iterators` are an implementation of `Iterable` objects like `Map`s, `Array`s or `string`s that allow end users to iterate over them using the `next` method
* To check if something has an `iterator`, you can do something like `"hello world"[Symbol.iterator]()` which will return a `StringIterator`
* Functions generally execute from start to finish (a `return` or a `throw`, for example) without pausing
* Generator functions execute from start to finish but pause at `yield`s
* When a Generator function is invoked, it returns a Generator object
  * This Generator object can be iterated through using the `next` method
  * Every call to `next` will continue execution until the next `yield`, at which point execution is suspended
  * `yield` returns an `object` with two keys - `value` and `done`
    * `value` is everything on the "right hand side" of the `yield`
      * empty `yield`s `return` `undefined`
    * `done` indicates whether the status of the generator can be executed further or not

## Passing arguments to the `next` method

* `yield`ing returns an `object` with the value to the right of the `yield`
* Passing back a `next` method call with a value replaces the previously `yield`ed expression value with the specified value

```javascript
function* test(i) {
  console.log(i);
  const j = 5 * (yield (i * 10));
  console.log(j);
  const k = yield (2 * j / 4);
  console.log(k);
  return (i + j + k);
}

const testGenerator = test(10);
// The value passed into next here does not do anything
// Since there's no previous yield to apply the value to
generator.next(20); // { value: 100, done: false }
// The 10 here replaces the value (i * 10)
// So now j is 5 * 10 => 50
generator.next(10); // { value: 25, done: false }
// The 5 here replaces the value (2 * j / 4) which was 25
// So now k is 5
// i + j + k = 10 + 50 + 5 = 65
generator.next(5); // { value: 65, done: false }
```

## Calling a function inside a Generator using a `yield`ed a value

```javascript
function logger(value) {
  console.log('Received value: ', value);
}

function* test() {
  yield
  logger(yield 'foobarbaz');
}

const testGenerator = test();
// Nothing to right of first yield so it returns undefined
testGenerator.next(); // { value: undefined, done: false }
// Returns "foobarbaz" which is right of first yield
testGenerator.next(); // { value: 'foobarbaz', done: false }
// Nothing is passed back via this next
// So previous yield expression evaluates to undefined
// Thus the logger method is called with undefined
testGenerator.next(); // { value: undefined, done: true }
// This will also console log "Received value: "
```

## Calling a function inside a Generator and `yield`ing the resultant value

```javascript
function getFoo() {
  return 'foo';
}

function* test() {
  const foo = yield getFoo();
  return foo;
}

const testGenerator = test();
// executes `getFoo`
// `getFoo` returns 'foo'
// 'foo' is yielded out by the test generator
testGenerator.next(); // { value: 'foo', done: false }
// Nothing is passed back to generator via the next
// Thus the yield expression evaluates to undefined
// Thus foo is assigned undefined
// And the generator outputs undefined
testGenerator.next(); // { value: undefined, done: true }
```

## `yield*`

* `yield*` delegates another generator function, synchronously completing it before moving on to the next line

```javascript
function* inner() {
  yield 2;
  yield 3;
  yield 4;
}

function* outer() {
  yield 1;
  yield* inner();
  yield 5;
}

const generator = outer();
generator.next(); // { value: 1, done: false }
generator.next(); // { value: 2, done: false }
generator.next(); // { value: 3, done: false }
generator.next(); // { value: 4, done: false }
generator.next(); // { value: 5, done: false }
generator.next(); // { value: undefined, done: false }
```

## `yield*` with a `return`

* Using a `yield*` to delegate to a `generator` that `return`s a value assigns the `return`ed value as the `yield` expression

```javascript
function* inner() {
  yield 2;
  yield 3;
  return 'bar';
}

function* outer() {
  yield 1;
  const innerValue = yield* inner();
  console.log('inner value is: ', innerValue);
  yield innerValue;
}

const generator = outer();
generator.next(); // { value: 1, done: false }
generator.next(); // { value: 'bar', done: false }
// console logs 'inner value is: bar'
generator.next(); // { value: 'bar', done: false }
generator.next(); // { value: undefined, done: true }
```

## `yield*` with an `Iterable`

```javascript
function* iterates() {
  yield* [1, 2];
  yield* 'Hi';
  yield* arguments;
}

const generator = iterates('jae', 'baebae');
generator.next(); // { value: 1, done: false }
generator.next(); // { value: 2, done: false }
generator.next(); // { value: 'H', done: false }
generator.next(); // { value: 'i', done: false }
generator.next(); // { value: 'jae', done: false }
generator.next(); // { value: 'baebae', done: false }
generator.next(); // { value: undefined, done: true }
```
