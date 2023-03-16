# [Master JavaScript Prototypes & Inheritance](https://codeburst.io/master-javascript-prototypes-inheritance-d0a9a5a75c4e)

* Prototypes are implemented via `__proto__` property on an object ("dunder proto" - double underscore prototype)
* `Function`s are objects and they have a `prototype` property - **distinct from `proto` property`**
* `__proto__` of `{}` is `Object.prototype`
* `__proto__` of `Object.prototype` is `null`
* Prototype chain inspects `__proto__` object for property (and then it's `__proto__` object for property) until it gets to `__proto__` object that is `null`
* [`__proto__` is object used to resolve lookups while `prototype` is object used to build `__proto__` when create object with `new`](https://stackoverflow.com/a/9959753/10039741)
* The `new` keyword when invoking a function sets the returned object's `__proto__` to reference the `prototype` property of the constructing function
* The `__proto__` of a function's `prototype` is `Object.prototype`
* This means that placing methods on function's `prototype` allows all instances access to those methods
* Benefit over attaching function in constructor is that when attaching function in constructor, each instance needs own reference to function
  * In prototype case, the instances share reference to same function
  * This can impact performance at scale
* Every function's `prototype` object has a `constructor` property that references the function itself
* When creating objects using `Object.create` the created object's `__proto__` will be equal to object used when calling `Object.create`
