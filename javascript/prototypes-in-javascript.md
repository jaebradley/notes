# [Prototypes in JavaScript](https://medium.com/better-programming/prototypes-in-javascript-5bba2990e04b)

* Every object created using the constructor function will have own copy of properties and methods
  * This can result in a lot of unnecessary memory that is alleviated by having a `prototype` object that is used to share methods
* Functions have a `prototype` property whose value is an object
  * This object has a `constructor` property, which points back to original function
* When object is created using constructor function, the object has a `__proto__` property (dunder proto) which points to the `prototype` object of the constructor function
  * Every object created using constructor function should have a dunder proto that is referentially equivalent to every other object created using constructor function
  * Adding properties and methods to the `prototype` object means that objects created using constructor function can use properties and methods
* Prototype chain
  * JS tries to find property on object
  * If property is not present, looks at dunder proto
  * Continues this until property is found, else will return `undefined`
* Prototype containing an array
  * Modifying array for an object (i.e. `push`ing a value to the array) will modify that array for all objects with that `prototype` object
* Define all object-specific properties inside constructor and all shared properties and methods inside the `prototype`

```javascript
//Define the object specific properties inside the constructor
function Human(name, age){
	this.name = name,
	this.age = age,
	this.friends = ["Jadeja", "Vijay"]
}
//Define the shared properties and methods using the prototype
Human.prototype.sayName = function(){
	console.log(this.name);
}
//Create two objects using the Human constructor function
var person1 = new Human("Virat", 31);
var person2 = new Human("Sachin", 40);

//Lets check if person1 and person2 have points to the same instance of the sayName function
console.log(person1.sayName === person2.sayName) // true

//Let's modify friends property and check
person1.friends.push("Amit");

console.log(person1.friends)// Output: "Jadeja, Vijay, Amit"
console.log(person2.friends)//Output: "Jadeja, Vijay"
```
