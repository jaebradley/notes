# [Chapter 4](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch4.md)

```javascript
class Vehicle {
	engines = 1

	ignition() {
		output( "Turning on my engine." )
	}

	drive() {
		ignition()
		output( "Steering and moving forward!" )
	}
}

class Car inherits Vehicle {
	wheels = 4

	drive() {
		inherited:drive()
		output( "Rolling on all ", wheels, " wheels!" )
	}
}

class SpeedBoat inherits Vehicle {
	engines = 2

	ignition() {
		output( "Turning on my ", engines, " engines." )
	}

	pilot() {
		inherited:drive()
		output( "Speeding through the water with ease!" )
	}
}
```

* `super` keyword is a way to refer to parent / ancestor of current class
* In traditional class-based languages, `super` also gives access to parent's constructor as constructor belongs to class
  * In JS, class belongs to constructor via `prototype`
* Child class gets copy of parent attributes - class inheritance implies copies

## Multiple inheritance

* Diamond problem
* `D` inherits from `B` and `C` which both inherit from `A`
* `B` and `C` both implement some common method
* How does `D` resolve the implementation of this common method?
* JavaScript does not provide a native mechanism for multiple inheritance

## Mixin / Explicit Pseudo-Polymorphism

```javascript
// vastly simplified `mixin(..)` example:
function mixin( sourceObj, targetObj ) {
	for (var key in sourceObj) {
		// only copy if not already present
		if (!(key in targetObj)) {
			targetObj[key] = sourceObj[key];
		}
	}

	return targetObj;
}

var Vehicle = {
	engines: 1,

	ignition: function() {
		console.log( "Turning on my engine." );
	},

	drive: function() {
		this.ignition();
		console.log( "Steering and moving forward!" );
	}
};

var Car = mixin( Vehicle, {
	wheels: 4,

	drive: function() {
		Vehicle.drive.call( this );
		console.log( "Rolling on all " + this.wheels + " wheels!" );
	}
} );
```

* A dumb way of doing mixins is to copy all methods / properties in source object to target object if they don't exist in the target object
  * Technically, these will *share* references and won't actually create new instances of shared functions, for example
* Prior to `ES6`, JavaScript did not have a way of distinguishing relative polymorphism
  * So in JavaScript, since `Car` and `Vehicle` both have a function with the same name, have to specify the particular object by name
  * So in order to have `Car.drive` reference `Vehicle.drive` it needs to call it explicitly.
  * To ensure correct `this` context, have to call it like `Vehicle.drive(this)`
  * This creates brittle manual / explicit linkages in every single function where you need this polymorphic relationship

> The result of such approaches is usually more complex, harder-to-read, and harder-to-maintain code. Explicit pseudo-polymorphism should be avoided wherever possible

### Mixing copies

* `Car` and `Vehicle` will share references to common functions - there isn't any *real* duplication
* If one of the `function` references were changed, then both `Vehicle` and `Car` would be affected
* Not much benefit from copying - could just define the property twice (once on each object)

### Parasitic Inheritance

* Basically, have "classic" JavaScript class
* Create new inherited class
* In class constructor, keep reference to parent class methods to inherit from
* Instantiate parent instance, and then override all necessary instance methods / properties using parent class method references

```javascript
// Classic JavaScript "class"
function Foo() {
  // Instantiate something
}

Foo.prototype.bar = function() {
  console.log('bar');
}

Foo.prototype.baz = function() {
  console.log('baz');
}

function Bar() {
  const fooInstance = new Foo();
  const fooBazReference = fooInstance.baz;

  fooInstance.baz = function() {
    fooBazReference.call(this);
  }

  return fooInstance;
}
```

### Implicit Mixin

```javascript
const Foo = {
  bar: function() {
    this.phrase = 'foobar';
    console.log('bar');
  }
}

Foo.bar(); // bar
Foo.phrase; // foobar

const Baz = {
  bar: function() {
    Foo.bar.call(this);
  }
}

Baz.bar(); // bar
Baz.phrase; // foobar
```

* Still pretty brittle since `Baz` still explicitly references `Foo`
