# [How ES6 Classes Really Work and How To Build Your Own](https://medium.com/@robertgrosse/how-es6-classes-really-work-and-how-to-build-your-own-fd6085eb326a)

* Each object has pointer to another object (it's prototype)
* When attempting to access property on object where no property exists with that key, it looks up key on prototype object, and returns prototype property if key exists
  * If key doesn't exist, it walks the prototype chain (i.e. the prototype's prototype, and it's prototype, etc.) until an object without a prototype is reached
* Function is JavaScript are defined by two internal methods `[[Call]]` and `[[Construct]]`
  * `Call` is what happens when object is invoked as a Function
  * `Construct` is what happens when function is invoked as constructor (i.e with `new` ahead of it)
* `Construct` will create a new object whose `Prototype` is the `prototype` property of the constructor function
  * The new object is bound to the `this` value
  * If constructor does not explicitly return an object, the constructor will evaluate to implicitly created `this`
* Every function has a prototype property that points to an object
  * This object has a `constructor` property which points to the original function
  * prototype property is different than function's `[[Prototype]]` property (which is `Function.prototype`)
* Constructor of the superclass is the `[[Prototype]]` of the subclass constructor
* prototype object of the superclass is the `[[Prototype]]`` of the subclass prototype object
* `super` works by accessing internal slot called `[[HomeObject]]`, which holds object that the function was originally defined within
  * For classes, this will be prototype object of the class (`Foo.prototype`)
  * `super.foo` => `[[HomeObject]].[[Prototype]].foo`
  * `HomeObject` is fixed at definition time and will not change even if you later assign the function to other objects
    * In next example, `HomeObject` points to `D.prototype`
    * `super` looks in `Prototype` of `D.prototype` which is `C.prototype`

```javascript
class A {
    foo() {return 'foo in A';}
}
class B extends A {
    foo() {return 'foo in B';}
}
class C {
    foo() {return 'foo in C';}
}
class D extends C {
    foo() {return super.foo();}
}

b = new B;
console.log(b.foo()); // foo in B

B.prototype.foo = D.prototype.foo
console.log(b.foo()); // foo in C
console.log(b instanceof C); // false
```

* Static methods are the same as regular methods except that they are defined as properties on the constructor function instead of on the prototype object
* When the `Construct` method of constructor function is invoked, the newly created object is bound to `this`
* Subclass constructors have no automatically created `this` - to get a `this` first need to call constructor of superclass via `super`
* The result of superclass constructor is then bound to local `this`, after which `this` is accessible in `subclass` constructor
