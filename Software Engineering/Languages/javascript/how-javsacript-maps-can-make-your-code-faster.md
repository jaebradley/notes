# [How JavaScript Maps Can Make Your Code Faster](https://medium.com/@bretcameron/how-javascript-maps-can-make-your-code-faster-90f56bf61d9d)

* Each key in an ordinary JavaScript object must be a `String` or a `Symbol`
* `Map`s allow functions, objects, and any other primitive types (like `NaN`) as object keys
* `for/in` on an object only allows iteration over enumerable non-`Symbol` properties and order is arbitrary
* `Map`s are a keyed collection, meaning they are directly iterable so the order of iteration is the same as the order of insertion
  * Use `for/of` or `forEach`
* To get size of object, must do `Object.keys.length` vs. `Map.size`
* `[['first', 1], ['second', 2]] <=> [...new Map([['first', 1], ['second', 2]])]`
  * Can easily go from `Map` to array using destructuring
* `ES2019` helper methods
  * `Object.fromEntries)` (`Map` to object)
  * `new Map(Object.entries(example))` (object to `Map`)
* `Map` is faster than object except when using `for` loop to create `Map`

