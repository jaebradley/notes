# [Arrow Functions](https://github.com/getify/You-Dont-Know-JS/blob/master/es6%20%26%20beyond/ch2.md#arrow-functions)

* Problems can arise with `this` as arrow functions have lexical `this` (i.e. they have `this` from next level)
* Arrow functions also have lexical `arguments` - they inherit from parent
  * Also inherit lexical `super` and `target.new`

```javascript
const foo = {
  bar: () => this.baz();
  baz: () => console.log('baz');
}

foo.bar(); // fails because `this` is global object which doesn't have a `baz` function
```

* When to refactor
  * Short, single-statement inline function expression which
    * `return`s
    * doesn't make a `this` reference
    * no self-reference (like recursion)
  * Short function that relies on `self = this` or `.bind(this)` can be refactored
* Everything else should be normal function expression
