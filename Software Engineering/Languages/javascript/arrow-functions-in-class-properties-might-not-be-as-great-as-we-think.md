# [Arrow Functions in Class Properties Might Not Be As Great As We Think](https://medium.com/@charpeni/arrow-functions-in-class-properties-might-not-be-as-great-as-we-think-3b3551c440b1)

```javascript
class A {
  constructor(name) {
    this.name = name;
  }

  handleClick = () => {
    console.log(`A handleClick with name: ${this.name}`);
  }
}
```

* This gets compiled by Babel with the `handleClick` arrow function instantiated in the constructor

```javascript
class A {
  constructor(name) {
    this.name = name;
    this.handleClick = () => {
      console.log(`A handleClick with name: ${this.name}`);
    }
  }
}
```

* `A.prototype.handleClick` is not a function
* The fact that arrow functions are instantiated in the constructor leads to some issues when mocking
  * This is because often times a mocking strategy is to mock / spy on prototype methods - by doing this, you can mock / spy on any objects that use this prototype
  * However, with instantiated functions, these are only available on a specific instance itself
* This constructor instantiation also poses a problem when inheriting
  * If `B` inherits from `A` and it has it's own `handleClick` arrow function that calls `super.handleClick`, `super.handleClick` is going to throw an exception
  * This is because `handleClick` isn't in the `prototype` object and thus `super.handleClick` can't be called
  * If `C` inherits from `A` and it does not have a `handleClick` arrow function but a class method that calls `super.handleClick`, `handleClick` will only execute the "original" `handleClick`
  * This is because when instantiating `C`, the creation of `handleClick` in the constructor overrides the the `handleClick` class method
    * `C.prototype.handleClick` will fail in the same way that `B` fails because `handleClick` isn't in the parent `prototype` objects
* Arrow function instance methods have not really been optimized by browser and so there's a severe performance penalty when it comes to using them
* With class methods, it's one method on the `prototype` object that's reused by all instances so JavaScript compiler can optimize
* But with instance methods, it's (obviously) different methods for each object
