# Functional Programming Paradigms In Modern JavaScript: Partial Application

* Partial application is a technique used to transform functions of higher arity (i.e. functions that take multiple arguments) into multiple functions that take less arguments
* A curried function will be split into many "chained" functions all taking exactly one argument - curried functions are more predictable this way
  * Partially applied functions do not have this limitation - can apply any number of arguments and depending on the arguments, the returned function will have different arity
* Using `bind` to partially apply
  * In the following example, `5` is the value for `x`, and `5` is the value for `y` and `bind` returns a new function with these values applied
  * So now, this new function will effectively add `10` to it's input

```javascript
const add = (x, y, z) => x + y + z;
const add10 = add.bind(null, 5, 5);
add10(7);
// 17
```

* A use-case for partial application is applying default arguments or where many of the same argument are applied over and over again

```javascript
const makePostRequest = (url, headers, data) => ajax({ url, headers, method: 'POST', data })

const saveComment = partial(
  makePostRequest,
  'some url',
  { some: 'headers' }
);

// somewhere in the app
saveComment('first comment');

// somewhere else in the app
saveComment('second comment');
```