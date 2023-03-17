# [Arrow Functions](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md#lexical-this)

* Arrow functions capture the `this` at call-time
* In the following example, since `foo` was bound to `obj1`, `bar` will also be bound to `obj1`
  * This can't be overridden by `new`

```javascript
function foo() {
	// return an arrow function
	return (a) => {
		// `this` here is lexically adopted from `foo()`
		console.log( this.a );
	};
}

var obj1 = {
	a: 2
};

var obj2 = {
	a: 3
};

var bar = foo.call(obj1);
bar.call(obj2); // 2, not 3!
```
