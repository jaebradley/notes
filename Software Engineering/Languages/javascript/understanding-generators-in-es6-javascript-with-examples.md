# [Understanding Generators in ES6 JavaSCript with Examples](https://codeburst.io/understanding-generators-in-es6-javascript-with-examples-6728834016d5)

* A generator is a function that can stop midway and then continue from where it stopped
  * It appears to be a function but _functions_ like an iterator
  * It will produce a sequence of results instead of a single value
* Analogy is reading, and then answering the doorbell, but setting a bookmark at the last page that was read. Then go back to reading after answering the doorbell.
* Generators return an object that has a `next` method
  * This object will have a `value` property and a `done` property
  * The `done` property will be `true` when the generator stops and `false` when generator will continue
* There's no guarantee that a generator will finish (generator that `yield`s out values in a `while(true)`)
* `return` sets the `done` property to `true`, after which the generator cannot generate any more values
  * A generator function returns a generator object, which is an iterator
  * Since it's an iterator, it can be used in `for-of` loops
* Generator functions will stop when it encounters the next `yield` after `next` is called on the generator object
  * The value that is returned by the generator object (in the `value` property) is the value to the right of the `yield`
* Every function implicitly returns `undefined` so if a `generator` doesn't have an explicit `return` it will return `{value: undefined, done: true}`
* Generators simplify implementing iterables since they are already iterables
  * Custom iterables often times require dealing with `Symbol.iterator`, and keeping track of internal state

```javascript
const iterableObj = {
  [Symbol.iterator]() {
    let step = 0;
    return {
      next() {
        step++;
        if (step === 1) {
          return { value: 'This', done: false};
        } else if (step === 2) {
          return { value: 'is', done: false};
        } else if (step === 3) {
          return { value: 'iterable.', done: false};
        }
        return { value: '', done: true };
      }
    }
  },
}
for (const val of iterableObj) {
  console.log(val);
}
// This
// is 
// iterable.

// The same way to implement this using generators
function * iterableObj() {
  yield 'This';
  yield 'is';
  yield 'iterable.'
}
```

* Generators can receive values when the `next` method is called with a value
  * This value will be used by the "left-hand" side of the `yield` (for example, if the `yield` is assigned a variable
* Generators have lazy evaluation, so for example, when generating a power series using generators, the entire power series doesn't need to be calculated in one go, but can be calculated one value at a time. This allows calculations to only happen when needed
