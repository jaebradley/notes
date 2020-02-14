# [Please Stop Using Classes In JavaScript And Become A Better Developer](https://medium.com/javascript-in-plain-english/please-stop-using-classes-in-javascript-and-become-a-better-developer-a185c9fbede1)

* `ES5` "classes" by setting values on `prototype` property
  * `Car.prototype.start = function...`
  * To do inheritance, set the `prototype` property of the "inheriting class" to a copy of the `prototype` property of the parent class
  * `SportsCar.prototype = Object.create(Car.prototype)` - `Object.create` creates a new object, with the object's `prototype` pointing to the object that was passed as an argument
  * `SportsCar.prototype.constructor = SportsCar` (where `SportsCar` is the constructor function which probably sets properties using `this`)
* The `prototype` property is used to walk the `prototype` chain (i.e. if `prototype` of current object does not have property, then the `prototype`'s `prototype` is checked, and so on and so forth)
* An object is simply a `Map` data structure with a somewhat sophisticated lookup procedure
* `class` keyword is simply syntactic sugar on top of `ES5` "class" implementation
* One of the great advantages / reasons for classes in the first place are private variables, which aren't really existent in JS
* Classes create a straight top-to-bottom order and make certain changes harder to implement due to this structure
