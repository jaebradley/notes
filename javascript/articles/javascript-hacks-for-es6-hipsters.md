# [JavaScript Hacks for ES6 Hipsters](https://hackernoon.com/javascript-hacks-for-es6-hipsters-67d633ce8ace)

## Use Array Destructuring to Swap Values

```javascript
let first = 'first';
let second = 'second';
[ first, second ] = [ second, first ];
console.log(first); // second
console.log(second); // first
```

## `Async` / `Await` with Destructuring

```javascript
const [ first, second ] = Promise.all([ firstPromise, secondPromise ]);
```

## Array Operations

```javascript
// sum values in array
const sum = (values) => values.reduce((a, b) => (a + b), 0);
console.log(sum([1, 2, 3, 4])); // 10
```

## Array Concatenation

```javascript
const one = [1, 2];
const two = [3, 4];
const three = [5, 6];

const combined = [...one, ...two, ...three]; // [1, 2, 3, 4, 5, 6]
```
