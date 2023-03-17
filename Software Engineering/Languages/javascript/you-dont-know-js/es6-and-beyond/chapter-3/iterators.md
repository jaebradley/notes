# [Iterators](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch3.md)

* An iterator is a structured pattern for returning one value at a time
* ES6 has introduced an implicit interface for iterators
* The interface for an iterator
  * `next` - a function that returns the next iterator result
  * `return` - a function that terminates execution of the iterator and returns an iterator result where the `done` property is set to `true`
  * `throw` - a function that terminates execution of the iterator, signals error, and returns iterator result
* The iterator result is actually a `IteratorResult` interface consisting of a
  * `value` and a `done` boolean that indicates whether the iterator has completed execution
  * Custom iterators can add more properties, if necessary
* `Iterable` interface that specifies way to produce an iterator
  * `@@iterator` - This special built-in `Symbol` that represents the method that can produce iterators

```javascript
var values = [1, 2, 3];

var iterator = values[Symbol.iterator]();

iterator.next(); // { value: 1, done: false }
iterator.next(); // { value: 2, done: false }
iterator.next(); // { value: 3, done: false }
iterator.next(); // { value: undefined, done: true }
```

## `for...of` loop

Here's the `for` equivalent of a `for...of` loop

```javascript
for (var value, result; (result = it.next()) && !result.done;) {
  value = result.value;
}
```

* `next` is called before `done` check
  * This means that if `result.done` is `true` then iteration does not continue
  * This is why the final intended iteration value should *not* include `done: true` - that value won't be part of iteration

## How to make an iterator an iterable

* In the iterator object that is returned specify a `Symbol.iterator` function that returns `this`

```javascript
var Foo = {
  [Symbol.iterator]() {
    return {
      // This allows this iterator to also be an iterable
      [Symbol.iterator]() {
        return this;
      }

      return {
        next: function() {
          // do some shit
        }
      }
    }
  }
}
```

## An iterator that iterates through a queue of actions

```javascript
var Tasks = {
  values: [],

  [Symbol.iterator]() {
    var values = this.values.slice();

    return {

      // This turns iterator into iterable
      [Symbol.iterator]() {
        return this;
      },

      next: function(...args) {
        if (values.length > 0) {
          return {
            // Take first value, apply arguments passed to next function
            value: values.shift()(...args),
            done: false,
          };
        }
        return {
          done: true,
        };
      }

      return: function() {
        values.length = 0;

        // Stop execution, stop ability to call next in future and indicate that termination should stop
        return {
          done: true,
        };
      }
    }
  }
}

Tasks.values.push(
  function firstStep(x) {
    console.log('First step: ', x);
    return x * 2;
  }
  function secondStep(x, y) {
    console.log('Second step: ', x, y);
    return x + y;
  }
)

var iterator = Tasks[Symbol.iterator]();

// First step: 1
// { value: 2, done: false }
iterator.next(1);

// Second step: 2, 3
// { value: 5, done: false }
iterator.next(2, 3);

// { value: undefined, done: true }
iterator.next();
```

### Iterator consumption

* Spread operator fully consumes iterator
* Array destructuring can also partially consume iterator
* Can see this with arrays

```javascript
var values = [1, 2, 3, 4, 5, 6];

var iterator = values[Symbol.iterator]();

var [first, second] = iterator;
var [third, ...rest] = iterator;

console.log(first); // 1
console.log(second); // 2
console.log(third); // 3
console.log(rest); // [4, 5, 6]
```
