# [Introspection](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch6.md#introspection)

```javascript
function Foo() {
	// ...
}
Foo.prototype.something = function(){
	// ...
}

var a1 = new Foo();

// later

if (a1 instanceof Foo) {
	a1.something();
}
```

* `Foo.prototype` is in the prototype chain of `a1` which means that `a1 instanceof Foo` returns `true`
* This would make people believe that `a1` is some instance of the `Foo` class - but `Foo` is just a function that has a property (`prototype`) which references an arbitrary object that `a1` is delegation-linked to
* `instanceof` seems to suggest that `a1` and `Foo` are related, but it's really whether `a1` and some arbitrary object on `Foo`, `Foo.prototype` are related
* If `Foo.prototype` is reassigned, then `a1` is no longer an `instanceof` `Foo`

```javascript
function Foo() { /* .. */ }
Foo.prototype...

function Bar() { /* .. */ }
Bar.prototype = Object.create( Foo.prototype );

var b1 = new Bar( "b1" );

// relating `Foo` and `Bar` to each other
Bar.prototype instanceof Foo; // true
Object.getPrototypeOf( Bar.prototype ) === Foo.prototype; // true
Foo.prototype.isPrototypeOf( Bar.prototype ); // true

// relating `b1` to both `Foo` and `Bar`
b1 instanceof Foo; // true
b1 instanceof Bar; // true
Object.getPrototypeOf( b1 ) === Bar.prototype; // true
Foo.prototype.isPrototypeOf( b1 ); // true
Bar.prototype.isPrototypeOf( b1 ); // true
```

* In the previous code, have to do `Bar.prototype instanceof Foo` because `Object.getPrototypeOf(Bar) !== Foo.prototype` (since `Object.create` creates a whole new object)
  * The prototype of that created object refers to `Foo.prototype` though
