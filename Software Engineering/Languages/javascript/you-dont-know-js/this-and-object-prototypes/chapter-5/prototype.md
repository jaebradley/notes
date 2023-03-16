# [Prototype](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch5.md#prototype)

* If a property can't be found on the object directly, the prototype link of the object will be followed until a matching property is found or the prototype change ends and undefined is returned
* Normally, the prototype chain ends with `Object.prototype`
  * Can create objects using `Object.create(null)` which has no `prototype`

## Setting Properties

```javascript
const someObject.a = 'b';
```

* If `a` is a regular data accessor property found on `someObject` the assignment just changes the value of the existing property
* If `a` is shadowed, i.e. it exists on `someObject` but also exists higher up the prototype chain
* Three scenarios when `b` is added to `someObject` but also exists higher up the prototype chain
  * If a normal data accessor is found anywhere higher up the prototype chain and is writable (i.e. `writable: true` property descriptor) then a new property is added directly on `someObject`
  * If a property is found and is read-only (i.e. `writable: false`) then neither the found property is set, nor is a shadowed property set
  * If a property is a setter, then the setter is called (but again, no shadowed property is created and the setter is not redefined)
* In the last two cases, in order to create a shadowed `b`, need to use `Object.defineProperty`
* The second case can be seen as the most surprising
  * The perspective is if you think of properties being "copied down" from inherited object to inherited object, if a higher-level object has a property that is non-writable then it would make sense that to enforce that non-writable property for "downstream" properties
  * The odd part is that while `=` setting is prohibited, still can override using `Object.defineProperty`

```javascript
// anotherObject does not have an 'a' property but instead, shadows the 'a' property of someObject
// anotherObject.a++ will increment someObject.a and then assign that to anotherObject.a
// Since anotherObject.a doesn't exist and someObject.a is a normal, writable, data accessor, then a is shadowed on anotherObject and 3 is now assigned to a
const someObject = {
  a: 1,
};

const anotherObject = Object.create(someObject);

someObject.hasOwnProperty('a'); // true
anotherObject.hasOwnProperty('a'); // false

anotherObject.a++;

someObject.a; // 1
anotherObject.a // 2

anotherObject.hasOwnProperty('a'); // true
```
