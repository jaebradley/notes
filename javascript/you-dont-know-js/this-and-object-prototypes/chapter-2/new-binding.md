# [`new` Binding](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md#new-binding)

* "constructor functions" are just regular functions that get "hijacked" by the use of `new` in their invocation
* No such thing as "constructor functions" only *constructor calls of functions*
* When a constructor call occurs (i.e. `new` + function call)
  * A new object is created
  * This object is prototype-linked
  * This object is set as the `this` binding for the function call
  * Unless the function returns it's own alternative object, the newly created object is automatically returned

```javascript
function foo(a) {
  this.a = a;
}

var bar = new foo('bar');
console.log(bar.a); // bar
```
