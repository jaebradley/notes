# [Input and Output](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md#input-and-output)

* Remember, an iterator is created by the generator function
* Calling `next` on this iterator will advance the function until the next `yield` or end of generator
* `next` returns an object with a `value` property, which is either the value `return`ed at the end of the generator function or the value when the generator is `yield`ed

```javascript
function *foo(x) {
	var y = x * (yield);
	return y;
}

var it = foo( 6 );

// start `foo(..)`
it.next();

var res = it.next( 7 );

res.value;		// 42
```

* `6` is the passed in parameter
* `next` starts generator
* Execution is paused at the `yield` expression
  * Basically, it requests the calling code to provide a result value for the `yield` expression
* When `next(7)` is called, it passes `7` back to the `yield` expression to be the value that gets multiplied to `x`
* The function resumes execution and `return`s the result (`42`) as the `value` property of the `result` object

## Two-way Message Passing

```javascript
function *foo(x) {
	var y = x * (yield "Hello");	// <-- yield a value!
	return y;
}

var it = foo( 6 );

var res = it.next();	// first `next()`, don't pass anything
res.value;				// "Hello"

res = it.next( 7 );		// pass `7` to waiting `yield`
res.value;				// 42
```
