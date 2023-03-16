# [Explicit Binding](https://github.com/getify/You-Dont-Know-JS/blob/master/this%20%26%20object%20prototypes/ch2.md#explicit-binding)

* `call` and `apply` methods where you can explicitly specify `this`

```javascript
function foo() {
  console.log(this.a);
}

var someObject = {
  a: 'a',
}

foo.call(someObject); // a
```

* `bind` returns a new function where the `this` context is set to the passed in argument

```javascript
function foo(something) {
	console.log( this.a, something );
	return this.a + something;
}

var obj = {
	a: 2
};

var bar = foo.bind( obj );

var b = bar( 3 ); // 2 3
console.log( b ); // 5
```
