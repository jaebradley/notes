# Objects

* An `object` is a container of properties where the property has a `string` name and any valid, defined value
* Objects are passed around by reference and never copied

```javascript
var foo = {};
var bar = foo;
bar.bar = 'bar'; // foo.bar is also 'bar'

var a = {}, b = {}, c = {}; // a, b, and c are all references to different objects
var a = b = c = {}; // a, b, and c are references to the same object
```

* Every object is linked to a prototype object from which it inherits properties
* All objects are ultimately linked to `Object.prototype`
* Changes to an object's properties never impact it's prototype's properties
* Prototype linkage is only used when retrieving a value for a property name
  * If the object does not have a defined value for a property name than a property lookup is done for the object's prototype object
  * If the prototype object does not have a defined value for the property name, than it's prototype is looked up, etc. etc. until the `Object.prototype` object is scanned - if no value is found for the property name, than `undefined` is returned
* When a property is added to a prototype object, that property is immediately available to all of the objects based on that prototype
* Deleting an object's property will allow prototype properties to shine through

```javascript
// Assume foo is an object with a property bar that is defined at it's object level and NOT it's prototype level
// Also assume that foo's prototype has a property bar that differs from the object's value for bar
foo.bar // 'not bar'
delete foo.bar
foo.bar // 'bar'
```
