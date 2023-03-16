# [Prototypal Inheritance](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md#prototypal-inheritance)

* Typical prototypal code that creates links

```javascript
function Foo(foo) {
  this.foo = foo;
}

Foo.prototype.name = function() {
  return this.foo;
}

function Bar(foo, bar) {
  Foo.call(this, foo);
  this.bar = bar;
}

Bar.prototype = Object.create(Foo.prototype);

Bar.prototype.label = function() {
  return this.bar;
}

const someBar = new Bar('foo', 'bar');

someBar.name(); // 'foo'
someBar.label(); // 'bar'
```

* Important part is `Bar.prototype = Object.create(Foo.prototype)`
* This links `Bar`'s prototype directly to `Foo`'s via a new object
* It's important to avoid
  * `Bar.prototype = Foo.prototype`
    * `Bar`'s prototype now references the same object as `Foo`'s prototype
    * This implies that when modifying `Bar`'s prototype object, you'll now start modifying `Foo`'s as well, which might have unintended consequences
    * If this is what you want, then you almost certainly don't need `Bar` and should just use `Foo` and keep code simpler
  * `Bar.prototype = new Foo()`
    * Since constructor call is used, if calling `new Foo` has any side-effects, it's called here vs. when `Bar` descendants are created
* Can also do `Object.setPrototypeOf(Bar.prototype, Foo.prototype)` in `ES6`
