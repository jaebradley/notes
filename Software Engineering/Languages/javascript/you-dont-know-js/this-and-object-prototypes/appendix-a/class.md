# [Appendix A: Class](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20&%20object%20prototypes/apA.md)

## Benefits of `class` syntax

* less or no prototype references
* Can use `extends` to link objects vs. having to `Object.create` or another way of changing the `prototype` reference
  * Can also easily extend built-ins like `Array` or `RegexP` in a "natural" way
  * `super` provides a mechanism for relative polymorphism (can directly refer to one level up)

## Drawbacks of `class` syntax

* `class` syntax might make you think that there is a class-based syntax in `JS` via `ES6`
* Unfortunately, `class` is just syntactic sugar on top of `prototype`
* `class` doesn't "copy" method definitions like in traditional class-based languages
* If you change a parent's method, all child classes and instances will be impacted
* Since this behavior is only reasonable if you know about delegation-based behavior, so question to ask is why are you choosing `class` syntax for something fundamentally different from classes

```javascript
class C {
	constructor() {
		this.num = Math.random();
	}
	rand() {
		console.log( "Random: " + this.num );
	}
}

var c1 = new C();
c1.rand(); // "Random: 0.4324299..."

C.prototype.rand = function() {
	console.log( "Random: " + Math.round( this.num * 1000 ));
};

var c2 = new C();
c2.rand(); // "Random: 867"

c1.rand(); // "Random: 432" -- oops!!!
```

* `class` doesn't allow instances to share properties, just methods
  * In order to share properties, need to use `prototype`
  * The issue with this approach is that it exposes the concept of `prototype` via implementation detail

```javascript
class Foo {
  constructor() {
    // This modifies shared state vs. creating a shadowed property on the instance
    // this.count++ would create a shadowed .count property on firstFoo and secondFoo objects
    Foo.prototype.count++;

    console.log(`The count is: ${this.count}`);
  }
}

Foo.prototype.count = 0;

const firstFoo = new Foo(); // The count is 1
const secondFoo = new Foo(); // The count is 2

firstFoo.count === secondFoo.count;
firstFoo.count === 2;
```

## `super` bind time

* In this case, you might expect `super` to be evaluated dynamically
  * So in this case, `FooBar.bar` would reference `Baz.bar`
  * However, `super` is bound at creation time from `[HomeObject].prototype` (where `HomeObject` is bound at creation time)
  * In the `FooBar` case below, `FooBar.bar` is resolving to `Foo.bar` because `FooBar.bar`'s `HomeObject` is `Foo`

```javascript
class Foo {
  bar() {
    console.log('Inside Foo.bar');
  }
}

class Bar extends Foo {
  bar() {
    super();
  }
}

const Baz = {
  bar: function() {
    console.log('Inside Baz.bar');
  }
}

const FooBar = {
  bar: Foo.prototype.bar.
}

Object.setPrototypeOf(FooBar, Baz);

FooBar.bar(); // 'Inside Foo.bar'
```
