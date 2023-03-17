# [Iterables](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md#iterables)

* An `iterable` is an `object` that contains an `iterator`
* This `iterator` is retrieved from the `iterable` by using `Symbol.iterator`

```javascript
var a = [1, 2, 3, 4];
var it = a[Symbol.iterator]();

it.next().value; // 1
it.next().value; // 2
it.next().value; // 3
it.next().value; // 4
```

* Before when the generator was defined like this `[Symbol.iterator]: function() { return this; }` this meant that `this` is now an `iterable` and an `iterator`
* Thus, it can be used in a `for...of` since when the loop looks up `Symbol.iterator` the function returns itself
