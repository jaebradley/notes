# [Introduction to Generators](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md#breaking-run-to-completion)

```javascript
var x = 1;

function *foo() {
	x++;
	yield; // pause!
	console.log( "x:", x );
}

function bar() {
	x++;
}

// construct an iterator `it` to control the generator
var it = foo();

// start `foo()` here!
it.next();
x;						// 2
bar();
x;						// 3
it.next();				// x: 3
```

* Calling the generator function `foo` does not execute `foo`, but returns an `iterator` that is used to control the execution of `foo`
* The first `next` starts the generator, which executes the `x++` and pauses at the `yield`
* `x` should be `2` since we incremented it
* `bar` (which is just a plain old function) increments `x` again
* `x` should now be `3` after `bar` incremented it
* The next `next` call resumes the generator, which `console.log`s `x`, which should be `3`
