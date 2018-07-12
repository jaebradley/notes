# [A Simple Guide to ES6 Iterators in JavaScript with Examples](https://codeburst.io/a-simple-guide-to-es6-iterators-in-javascript-with-examples-189d052c3d8e)

* How would you iterate over

```javascript
const myFavoriteAuthors = {
  allAuthors: {
    fiction: [
      // some
    ],
    scienceFiction: [
      // more
    ],
    fantasy: [
      // authors
    ],
  },
}
```

* Using `for...of` results in

```javascript
for (let author of myFavoriteAuthors) {
  console.log(author);
}

// TypeError: {} is not iterable
```

## Iteration Protocol

* Standardized iteration protocol using `Symbol.iterator`
  * `Symbols` offer names that are unique and cannot clash with other property names
  * `Symbol.iterator` returns an object called an `iterator`
  * has a method called `next`
    * `next` returns object with `value` and `done` keys
    * `value` specifies the current value
    * `done` is a `boolean` that represents whether all values have been fetched

## Example Iterable Implementation

```javascript
const iterable = {
  [Symbol.iterator]() {
    let step = 0;
    const iterator = {
      next() {
        step++;

        if (step === 1) {
          return { value: 'This', done: false };
        }

        if (step ==== 2) {
          return { value: 'is', done: false };
        }

        if (step === 3) {
          return { value: 'iterable', done: true };
        }

        return { value: undefined, done: true };
      }
    }
    return iterator;
  }
}
const iterator = iterable[Symbol.iterator]();
iterator.next() // { value: 'This', done: false }
// etc.
```

## Iterables in JavaScript

* `for-of` loop requires an iterable
  * Keeps calling `next` until `done` is `true`
* Destructuring arrays

```javascript
const values = [1, 2, 3, 4, 5];
const [one, ,three, ,five] = values;

// equivalent
const iterator = values[Symbol.iterator]();
const one = iterator.next().value;
// etc.
```

* Spread operator

```javascript
const values = [1, 2, 3, 4, 5];
const newValues = [ 0, ...values, 6];

// equivalent
const iterator = values[Symbol.iterator()];
const newValues = [0];
for (let next = iterator.next(); next.done !== true; nextValue = iterator.next()) {
  newValues.push(nextValue.value);
}
newValues.push(6);
```
