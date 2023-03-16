# [Fundamental Object Design Patterns In JavaScript](https://medium.com/@severinperez/javascript-weekly-fundamental-object-design-patterns-31453f68427f)

## Factory Object Creation Pattern

* Factory function takes some inputs and outputs objects that take into account the inputs
  * Also means that shared methods / shared behavior are copied / recreated for every object created by the factory function, which takes up valuable space
  * So in the example, the `introduce` method is effectively shared behavior but the method is created each time the object is generated

```javascript
function makeRobot(name, job) {
  return {
    name: name,
    job: job,
    
    introduce: function() {
      console.log("Hi! I'm " + this.name + ". My job is " + this.job + ".");
    },
  };
}

var bender = makeRobot("Bender", "bending");
bender.introduce();   // Hi! I'm Bender. My job is bending.
```

## Constructor Pattern

* Use `new` + a function + `this` to return an object with properties specified by using `this`
* Still creating a new method for each instance / object that's returned from constructor

## Pseudo-Classical

* Takes advantage of prototype chain where a property is inspected on an object's `prototype` property (which is an `object`) and if not found, inspect it's `prototype` property, and so on
* So by defining a property on an object's `prototype` property, that property is defined on all objects that share that `prototype` object
* Combining the `constructor` pattern with the `prototype` pattern, means that methods don't have to be created on each instance, but can be shared by defining it on the `constructor`'s `prototype` object


```javascript
function Robot(name, job) {
  this.name = name;
  this.job = job;
}

Robot.prototype.introduce = function() {
  console.log("Hi! I'm " + this.name + ". My job is " + this.job + ".");
};
```

* Downside is that this gives the appearance of a "traditional" class-based system when JavaScript doesn't really have such a thing

## Object Linked To Other Object

* Create object blueprint and then use `Object.create` to create copies of that object
  * `Object.create` takes an object as an argument, and the returned object has the input object set as the output object's `prototype`
* This object blueprint has an `init` function that acts as kind've a constructor

```javascript
var Robot = {
  init: function(name, job) {
    this.name = name;
    this.job = job;
  },
  
  introduce: function() {
    console.log("Hi! I'm " + this.name + ". My job is " + this.job + ".");
  },
};

var bender = Object.create(Robot);
bender.init("Bender", "bending");
bender.introduce();   // Hi! I'm Bender. My job is bending.
console.log(Object.getPrototypeOf(bender) === Robot);  // true

var wallE = Object.create(Robot);
wallE.init("Wall-E", "trash collection");
wallE.introduce();    // Hi! I'm Wall-E. My job is trash collection.
console.log(Object.getPrototypeOf(wallE) === Robot);  // true
```