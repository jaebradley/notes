# [Promise Concurrency in Generators](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md#promise-concurrency-in-generators)

* Problem with following code is that each request runs synchronously, whereas they should be running concurrently

```javascript
function *foo() {
	var r1 = yield request( "http://some.url.1" );
	var r2 = yield request( "http://some.url.2" );

	var r3 = yield request(
		"http://some.url.3/?v=" + r1 + "," + r2
	);

	console.log( r3 );
}

// use previously defined `run(..)` utility
run( foo );
```

* To have them run concurrently in a generator, make both requests and then wait until both promises resolve
  * If `p1` resolves first, the `yield p1` resumes first and then waits for `yield p2`
  * If `p2` resolves first, the `yield p2` waits for `yield p1` to resolve

```javascript
function *foo() {
	// make both requests "in parallel"
	var p1 = request( "http://some.url.1" );
	var p2 = request( "http://some.url.2" );

	// wait until both promises resolve
	var r1 = yield p1;
	var r2 = yield p2;

	var r3 = yield request(
		"http://some.url.3/?v=" + r1 + "," + r2
	);

	console.log( r3 );
}

// use previously defined `run(..)` utility
run( foo );
```

* Asynchrony and Promises are an implementation detail of a generator
* We should intentionally abstract such details away from our generator code so that they don't clutter up the higher-level task expression
