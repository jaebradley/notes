# [Garbage Collection](https://javascript.info/garbage-collection)

* Memory management in JavaScript is based on reachability
* "root" values are values that cannot be deleted (and thus are inherently always reachable)
  * Variables for current function
  * Or if function call is nested, variables and function arguments used in chained nesting of function calls
  * Global variables
* Any other value is reachable if it can be referenced from a root value

```javascript
let someGlobalObject = {
  blahblahblah: 'foobar'
};

someGlobalObject = null;
```

* `someGlobalObject` global variable referenced an object
* The object / the `blahblahblah` property inside the object is no longer reachable once the variable was re-assigned to `null`

```javascript
let someGlobalObject = {
  blahblahblah: 'foobar'
};

let anotherGlobalObject = someGlobalObject;

let someGlobalObject = null;
```

* Object is still reachable via `anotherGlobalObject` variable so it's still stored in memory
* If an object has no references to it, it is not reachable
  * Even if it references other reachable objects
  * Only "incoming" references make an object reachable
* Mark and sweep
  * GC identifies roots and remembers them
  * Then it visits all their references and marks them as well
  * Continues this process until every roots-reachable reference is visited
  * All objects that have not been marked / remembered are removed