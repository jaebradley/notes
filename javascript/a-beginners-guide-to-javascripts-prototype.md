# [A Beginner's Guide to JavaScript's Prototype](https://medium.freecodecamp.org/a-beginners-guide-to-javascript-s-prototype-9c049fe7b34)

In this example, we're going to have an `Animal` constructor that takes in a `name` and `energy` parameters to create `animal` instances.

These `anima`l instances should have `eat`, `sleep`, and `play` methods.

## Approach 1: Functional Instantiation

You can do this in a function like

```javascript
function Animal(name, energy) {
  let animal = {};

  animal.name = name;
  animal.energy = energy;

  animal.eat = function() {
    // some function
  }

  animal.sleep = function() {
    // some function
  }

  animal.play = function() {
    // some function
  }

  return animal;
}
```

Downside to this approach is that each method (`eat`, `sleep`, `play`) are generic and don't need to be recreated for each instance (this wastes memory).

## Approach 2: Functional Instantiation with Shared Methods via Object

What if all the shared methods were themselves contained in an object? And then the `animal` instances simply referenced the methods on the object?

```javascript
const animalMethods = {
  eat: function() {
    // some function
  },
  sleep: function() {
    // some function
  },
  play: function() {
    // some function
  },
};

function Animal(name, energy) {
  let animal = {};

  animal.name = name;
  animal.energy = energy;

  animal.eat = animalMethods.eat;
  animal.sleep = animalMethods.sleep;
  animal.play = animalMethods.play;

  return animal;
}
```

## Approach 3: Functional Instantiation using Object.create

`Object.create` takes an `object` as an argument and returns an `object`. This returned `object` will delegate to the passed-in `object` argument if it cannot find a property.

```javascript
const foo = {
  bar: 'bar',
};

const baz = Object.create(foo);

console.log(baz.bar); // 'bar'

baz.bar = 'har';

console.log(baz.bar); // 'har'
```

Can apply `Object.create` to instantiation by initializing the instance using `Object.create(animalMethods)`. This way, the `animal` instance will appropriately delegate `eat`, and `sleep`, and `play`.

```javascript
function Animal(name, energy) {
  const animal = Object.create(animalMethods);

  animal.name = name;
  animal.energy = energy;

  return animal;
}
```

## Approach 4: Prototypal Instantiation

Using `animalMethods` works great, but it's a pain to maintain `animalMethods` to share methods across instances. The `prototype` property on every object allows the ability to share behavior (like `animalMethods` across instances).

The `prototype` property just points to an object where shared methods and properties can be stored.

```javascript
function Animal(name, energy) {
  const animal = Object.create(Animal.prototype);

  animal.name = name;
  animal.energy = energy;

  return animal;
}

Animal.prototype.eat = function() {
  // some function
}

Animal.prototype.sleep = function() {
  // some function
}

Animal.prototype.play = function() {
  // some function
}
```

Invoking a constructor with `new` actually does the `Object.create` automatically (using `this`) - using `new` is called the *pseudoclassical* pattern.

The `class` syntax is just sugar over the pseudoclassical pattern.

## Static Methods

You can add a `static` method to a `class` like

```javascript
class Animal {
  static someStatic() {
    // do something
  }
}
```

You can also add a `static` method using `ES5` doing

```javascript
Animal.someStatic = function() {
  // do something
}
```

## `prototype` facts

* By default, the `prototype` object will have a `constructor` property which points to the original function or the class that the instance was created
* `Object.getPrototypeOf(animalInstance) === Animal.prototype`
  * This is the way `instanceof` works
