# [Generator Delegation](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md#generator-delegation)

* Can execute a generator from inside a generator
* The "child" generator allows the "parent" generator to take over control of the child's iterator
* Note how in the example, the third `next` will start `foo` and iterate through `foo`'s `yield`s

```javascript
function *foo() {
	console.log( "`*foo()` starting" );
	yield 3;
	yield 4;
	console.log( "`*foo()` finished" );
}

function *bar() {
	yield 1;
	yield 2;
	yield *foo();	// `yield`-delegation!
	yield 5;
}

var it = bar();

it.next().value;	// 1
it.next().value;	// 2
it.next().value;	// `*foo()` starting
					// 3
it.next().value;	// 4
it.next().value;	// `*foo()` finished
					// 5
```

## Message Delegation

```javascript
function *foo() {
	console.log( "inside `*foo()`:", yield "B" );

	console.log( "inside `*foo()`:", yield "C" );

	return "D";
}

function *bar() {
	console.log( "inside `*bar()`:", yield "A" );

	// `yield`-delegation!
	console.log( "inside `*bar()`:", yield *foo() );

	console.log( "inside `*bar()`:", yield "E" );

	return "F";
}

var it = bar();

console.log( "outside:", it.next().value );
// outside: A

console.log( "outside:", it.next( 1 ).value );
// inside `*bar()`: 1
// outside: B

console.log( "outside:", it.next( 2 ).value );
// inside `*foo()`: 2
// outside: C

console.log( "outside:", it.next( 3 ).value );
// inside `*foo()`: 3
// inside `*bar()`: D
// outside: E

console.log( "outside:", it.next( 4 ).value );
// inside `*bar()`: 4
// outside: F
```

* Note the `it.next(3)` call
  * The `3` gets passed to the waiting `yield "C"` inside `foo`
  * `foo` unblocks and returns `"D"` to the `yield` in `bar`
  * `bar` console log `"D"` and then moves on to the next `yield`, which returns `E` to the iterator

## Exception Delegation

* Note how `it.throw(2)` sends an error to `bar`, which delegates the message to `foo`
  * `foo` gracefully catches the error and then moves on to the next `yield` which sends `"C"` back as the return value
* The `"D"` value that is thrown from inside `*foo` propagates to `*bar` which catches it and and console logs the output
  * It moves onto the next `yield` which is why it sends back `"E"` as the return value
* Next exception inside `baz` isn't caught, though it's caught outside the generator
* Now `bar` and `baz` should be set to a completed state and should not be able to get `"G"` value out with any `next` calls - should just return `undefined` for value

```javascript
function *foo() {
	try {
		yield "B";
	}
	catch (err) {
		console.log( "error caught inside `*foo()`:", err );
	}

	yield "C";

	throw "D";
}

function *bar() {
	yield "A";

	try {
		yield *foo();
	}
	catch (err) {
		console.log( "error caught inside `*bar()`:", err );
	}

	yield "E";

	yield *baz();

	// note: can't get here!
	yield "G";
}

function *baz() {
	throw "F";
}

var it = bar();

console.log( "outside:", it.next().value );
// outside: A

console.log( "outside:", it.next( 1 ).value );
// outside: B

console.log( "outside:", it.throw( 2 ).value );
// error caught inside `*foo()`: 2
// outside: C

console.log( "outside:", it.next( 3 ).value );
// error caught inside `*bar()`: D
// outside: E

try {
	console.log( "outside:", it.next( 4 ).value );
}
catch (err) {
	console.log( "error caught outside:", err );
}
// error caught outside: F
```
