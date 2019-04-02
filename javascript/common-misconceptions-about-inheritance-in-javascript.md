# [Common Misconceptions About Inheritance in JavaScript](https://medium.com/javascript-scene/common-misconceptions-about-inheritance-in-javascript-d5d9bab29b0a)

* Class inheritance uses a blueprint (i.e. a class) to create instances and then invoke methods on those instances
  * Subclasses create class hierarchies
  * Changing base classes has rippling side-effects that can lead to brittle behavior
* Prototypal inheritance uses instances to beget other instances
  * Set the prototype of one instance to delegate to another exemplar object
  * Can also copy properties from exemplar object to new instance

## Delegate prototypes

* `animal` is the delegate prototype object
* `mouse` is an instance
* If a property is not on the `mouse` object, the prototype chain (i.e `animal`) is inspected to see if any object in the chain has the property
* `Object.assign(target, ...sources)` will copy all _enumerable, own properties_ from input source objects to destination object. Last wins.

```javascript
let animal = {
  animalType: 'animal',

  describe () {
    return `An ${this.animalType}, with ${this.furColor} fur,
      ${this.legs} legs, and a ${this.tail} tail.`;
  }
};

let mouse = Object.assign(Object.create(animal), {
  animalType: 'mouse',
  furColor: 'brown',
  legs: 4,
  tail: 'long, skinny'
});
```

## What does `new` do

* Create a new instance
* Bind `this` to instance
* Point the instance's `[[Prototype]]` delegate to the object referenced by constructor function's `prototype` property
* Point the instance's `constructor` property to the constructor function
