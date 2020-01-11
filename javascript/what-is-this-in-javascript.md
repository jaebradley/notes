# [What is `this` in JavaScript](https://blog.bitsrc.io/what-is-this-in-javascript-3b03480514a7)

## In Global Environment

* `this` refers to the `global` object when using `node` REPL
* Inside a JavaScript file, `this` will refer to `module.exports` and _not_ global

## Inside functions

* In non-strict mode, `this` will equal `global` object when used inside a function
* In strict mode, `this` will equal undefined when used inside function
* Inside constructors (i.e. when a function is called with the `new` operator) a new object is created and set as function's `this` value
  * Object is implicitly returned from function unless another object is returned explicitly

## Inside methods

* `this` refers to the enclosing object (i.e. the "receiver" of the function call)

```javascript
const person = {
  name: "Jaebaebae",
  sayMyName() {
    console.log(`My name is ${this.name}`);
  },
};

person.sayMyName(); // My name is Jaebaebae
```

* If the above method is referenced by a variable and then called, `undefined` will be outputted because `this` refers to `global` object and `global.name` will be `undefined`

```javascript
const testMethod = person.sayMyName;
testMethod(); // undefined
```

## Using `call` and `apply` and `bind`

* Can set a function's `this` value using `call` and `apply`
* Can pass `call` and `apply` an object (for example) that will be used as `this` value
* If using `call` and `apply` in non-strict mode and pass `null` or `undefined`, that will be ignored by JS engine which is why should almost always write in strict mode
* Another way to keep reference to correct `this` is by using `bind` and passing it intended `this` value
  * `bind` returns a new function with the `this` value set to whatever was specified
  * `this` cannot be changed by additional calls to `call` or `apply` methods

## Inside arrow functions

* An arrow function uses `this` value from enclosing execution context when created
* Arrow function's `this` cannot be changed by `apply` or call` or `bind`
* Arrow function cannot be used as a constructor

```javascript
// this will refer to global object and NOT counter object (due to when setInterval is executed and the context in which it's executed)
const counter = {
  count: 0,
  delayedIncrease() {
    setInterval(function() {
      console.log(++this.count);
    }, 1000);
  },
};

// this will refer to counter object since arrow function uses this from surrounding context
const counter = {
  count: 0,
  delayedIncrease() {
    setInterval(() =>{
      console.log(++this.count);
    }, 1000);
  },
};
```

## Inside classes

* JavaScript classes are implicitly in strict mode
* `constructor` function `this` refers to newly created object
* Can still lose reference to "receiver" when an instance method is reassigned to a variable and that variable is executed
