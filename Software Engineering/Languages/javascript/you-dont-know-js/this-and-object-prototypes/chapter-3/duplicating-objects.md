# [Duplicating Objects](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch3.md#duplicating-objects)

```javascript
function anotherFunction() { /*..*/ }

var anotherObject = {
	c: true
};

var anotherArray = [];

var myObject = {
	a: 2,
	b: anotherObject,	// reference, not a copy!
	c: anotherArray,	// another reference!
	d: anotherFunction
};

anotherArray.push( anotherObject, myObject );
```

* shallow copy would basically be another object that shares references where copying non-primitive values is not possible
* deep copy is more complicated
  * How to deal with circular references? (In this case `anotherArray` references `myObject` which has `anotherArray`)
* You can use `JSON.stringify/parse` for objects that are `JSON`-safe
* `Object.assign`
  * takes a target object as the first parameter
  * takes at least one source object
  * Iterates over all enumerable, owned / immediately present key on each source object and copies this key and it's value, by assignment only to the target object
  * Property descriptors are *not* preserved
