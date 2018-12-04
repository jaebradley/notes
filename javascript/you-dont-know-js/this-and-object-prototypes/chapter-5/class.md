# [Class](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md#class)

* All functions, get a public, non-enumerable property called `prototype` which points to an arbitrary object
* Each object created via the `constructor` ends up prototype linked to this `prototype` object

```javascript
function Foo() {
  // stuff
}

const someObject = new Foo();
Object.getPrototypeOf(someObject) === Foo.prototype;
```

* In class-oriented languages, instances are created by copying behavior from some "plan" (i.e. class)
* In JavaScript, no such copying is done
  * There aren't multiple instances of a class, but rather, multiple objects that are linked to a common object via `prototype` chains
* `new Foo` is an accidental side-effect / round-about way of getting to end goal of two objects that are linked together
* Any function can be turned into a "constructor call" when the `new` operator is placed in front of the function

```javascript
// new NothingSpecial creates an object (a) but nothing about NothingSpecial itself is a constructor
function NothingSpecial() {
	console.log( "Don't mind me!" );
}

const a = new NothingSpecial();
// "Don't mind me!"

a; // {}
```

```javascript
// Tempting to think that bar and baz got copy of name, but that's not what happens
// Instead, bar and baz delegate up the prototype chain until they get to Foo's name property
function Foo() {
  this.a = 'foo';
}

Foo.prototype.name = function() {
  return this.a;
}

const bar = new Foo();
const baz = new Foo();

bar.name(); // 'foo'
baz.name(); // 'foo'
```

* `bar.constructor === Foo` from previous example, but `.constructor` property is also delegated via prototype chain
* In reality, `bar`'s `constructor` property is delegated up to `Foo.prototype`, which has a `constructor` property that points at `Foo`
* However, this `constructor` property can change

```javascript
// someFoo doesn't have constructor property so it delegates to Foo
// Foo doesn't have constructor property since the default prototype object was overridden so it delegates to Object
// Object has a constructor property that points to the built-in Object function
function Foo() {

}

Foo.prototype = {
  // some object
};

const someFoo = new Foo();
someFoo.constructor === Foo; // false
someFoo.constructor === Object; // true
```
