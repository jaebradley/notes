# [Function Scopes](https://github.com/getify/You-Dont-Know-JS/blob/master/scope%20%26%20closures/ch3.md#scope-from-functions)

```javascript
function foo(a) {
  var b = 2;

  function bar() {
    // some shit
  }

  var c = 3;
}
```

* Identifiers are `a`, `b`, `bar`, and `c`
* It doesn't matter where these declarations in a scope, the variable or function belongs to the containing scope bubble
* `bar` has it's own scope bubble
* Global scope has own scope bubble, and has one identifier attached to it, `foo`
* `a`, `b`, `c`, `bar`, and `foo` are all accessible inside `foo`'s scope and also accessible inside `bar`
* Only `foo` is accessible in the global scope

## Hide Scope Using Functions

* Because functions have their own scopes, you can wrap code in functions to "isolate" the code from outside influences
* This includes namespace collision avoidance
  * In the following example, setting `i` to `3` in `bar` fixes the `i` value in the `for` loop, thus creating an infinite loop

```javascript
function foo() {
	function bar(a) {
		i = 3; // changing the `i` in the enclosing scope's for-loop
		console.log( a + i );
	}

	for (var i=0; i<10; i++) {
		bar( i * 2 ); // oops, infinite loop ahead!
	}
}

foo();
```

## `IIFE`s

* An immediately invoked function expression, is a function that, well, gets immediately invoked and by doing so, wraps it's internals in function scope
* `IIFE`s won't pollute the containing scope's namespace

```javascript
// Example of an IIFE
var a = 2;

(function foo(){

	var a = 3;
	console.log( a ); // 3

})();

console.log( a ); // 2
```

* Another `IIFE` pattern passes the executable function as an argument to the actual `IIFE`
* This pattern is used by `UMD` (Universal Module Definition)
* The following `IIFE` takes a `def` function as an argument, and passes that `def` function the `window` object

```javascript
var a = 2;

(function IIFE( def ){
	def( window );
})(function def( global ){

	var a = 3;
	console.log( a ); // 3
	console.log( global.a ); // 2
});
```
