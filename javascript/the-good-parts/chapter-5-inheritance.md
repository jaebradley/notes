# Inheritance

## Pseudoclassical

* When a function object is created, the `Function` constructor produces the function object that runs some code like

```javascript
this.prototype = { constructor: this };
```

* This `prototype` object is the place where inherited traits are deposited
  * The `constructor` property is not useful
* Every function gets a `prototype` object because the language does not provide a way of determining which functions are intended to be used as constructors
* When a function is invoked with the `new` prefix (i.e the constructor invocation pattern), it modifies the way in which the function is executed

```javascript
// What the new operator would look like if it was implemented as a method

Function.method('new', function() {
  // Create new object that inherits from constructor's prototype
  var that = Object.create(this.prototype);

  // Invoke constructor, binding this to new object
  var other = this.apply(that, arguments);

  // If other is not an object, return that
  return (typeof other === 'object' && other) || that;
});
```

```javascript
// What this looks like in practice

// Create constructor
var Mammal = function(name) {
  this.name = name;
};

// Add getter for name property
Mammal.prototype.getName = function() {
  return this.name;
};

// Add getter for saying property that defaults to empty string
Mammal.prototype.says = function() {
  return this.saying || '';
};

// Make instance
var myMammal = new Mammal('Herb the Mammal');
myMammal.getName(); // Herb the Mammal

// Make Cat class that extends from Mammal
var Cat = function (name) {
  this.name = name;
  this.saying = 'meow';
};

// Replace constructor function's prototype object with instance of Mammal
// This will inherit properties from Mammal
Cat.prototype = new Mammal();

// Update prototype with purr and updated getName method
Cat.prototype.purr = function(n) {
  var i, s = '';
  for (i = 0; i < n; i += 1) {
    if (s) {
      s += '-';
    }
    s += 'r';
  }
  return s;
};

Cat.prototype.getName = function() {
  return this.says() + ' ' + this.name + ' ' + this.says();
};

var myCat = new Cat('Henrietta');
// myCat gets says from Mammal prototype
myCat.says(); // meow

// myCat gets purr method from prototype augmentation
myCat.purr(5); // 'r-r-r-r-r'

// myCat gets getName from prototype augmentation
myCat.getName(); // meow Henrietta meow
```

* All properties are public
* There is no access to `super` methods
* If the `new` prefix is forgotten, then `this` will not be bound to a new object but will be bound to the global object, so instead of augmenting your new object, you will be clobbering global variables (and there are no runtime or compile warnings)

## Prototypal

* Can replicate the `Mammal` / `Cat` example without using constructors and only using object literals

```javascript
var myMammal = {
  name: 'Herb the Mammal',
  getName: function() {
    return this.name;
  },
  says: function() {
    return this.saying || '';
  },
};

var myCat = Object.create(myMammal);
myCat.name = 'Henrietta';
myCat.saying = 'meow';
myCat.purr = function(n) {
  var i, s = '';
  for (i = 0; i < n; i += 1) {
    if (s) {
      s += '-';
    }
    s += 'r';
  }
  return s;
};
myCat.getName = function() {
  return this.says() + ' ' + this.name + ' ' + this.says();
};
```

* However, this strategy of reuse also suffers from lack of privacy

## Functional

* Create a new object
* Define private variables or methods, if necessary
* Augment the new object with methods - methods will have privileged access to private parameters and variables
* Return the new object

```javascript
// Pseudocode
var constructor = function(spec, my) {
  var that, other, private, instance, variables;

  // Container of secrets that are shared by the constructors in the inheritance chain
  my = my || {};

  // Add shared variables and functions to my

  that = {};

  // Add public interface to that

  return that;
}
```

* When augmenting the new object with methods, define the method as a private method and then assign the method like

```javascript
var methodical = function() {
  console.log('methodical');
};

that.methodical = methodical;
```

* This way, if `that.methodical` is overwritten, any other methods that depend on the `methodical` method can call `methodical` instead of `that.methodical` since `methodical` should continue to work the same way

```javascript
// Replicate Cat / Mammal example

var mammal = function(spec) {
  var that = {};
  that.getName = function() {
    return spec.name;
  };
  that.says = function() {
    return spec.saying || '';
  };
  return that;
}

// mammal instance
var myMammal = mammal({ name: 'Herb' });

var cat = function(spec) {
  spec.saying = spec.saying || 'meow';

  // Delegating to mammal constructor
  var that = mammal(spec);

  that.purr = function(n) {
    var i, s = '';
    for (i = 0; i < n; i += 1) {
      if (s) {
        s += '-';
      }
      s += 'r';
    }
    return s;
  };
  that.getName = function() {
    return that.says() + ' ' + spec.name + ' ' + that.says();
  };

  return that;
};

var myCat = cat({ name: 'Henrietta' });
```
