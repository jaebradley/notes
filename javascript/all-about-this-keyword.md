# [All About "this" Keyword](https://codeburst.io/all-about-this-and-new-keywords-in-javascript-38039f71780c)

* `this` refers to an object, the object that is executing the current bit of javascript code
* Understanding `this` requires understanding how, when, and from where function is called
  * Does not matter how and where function is defined or declared

## Implicit Binding

* If a function gets called in the global execution context references `this`, `this` refers to the global context
  * Unless in strict mode, then default value of `this` is `undefined`
* If a function is assigned as a property in an object, and that object calls the method, if the method references `this`, `this` will refer to the object

## Explicit Binding

* `call` and `apply` take a context argument that represents the `this` in the execution of the function

```javascript
function printName() {
  console.log(this.name);
}

const someObject = { name: 'foobar' };
printName.call(someObject); // 'foobar'
```

## `new` keyword in JavaScript

* When `new` is called
  * function call turns into constructor call
  * New object is created
  * New object is linked to `prototype` property of the function
  * New object is bound as `this` keyword for execution context of function call
  * If function doesn't return anything it implicitly returns `this` object

## Precedence of `this` keyword bindings

1. Checks if function called with `new`
2. Checks if function is called with `call` or `apply`
3. Checks if function is called via an object (which the function will use it's context)
4. Uses global context (or `undefined` in strict mode)

