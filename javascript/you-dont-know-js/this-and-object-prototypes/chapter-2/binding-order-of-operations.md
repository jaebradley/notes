# [Binding Order of Operations](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md#determining-this)

* Is the function called with a `new` - `this` is a newly constructed object
  * **new binding**
* Is the function called with `call` or `apply` - `this` is the specified object
  * **explicit binding**
* Is the function called in the context of an object - `this` is the context object
  * **implicit binding**
* Else, it's a **default binding** - `this` is `undefined` in `strict` mode or the `global` object
