# [`Promise` Trust](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch3.md#promise-trust)

* Call the callback too early
  * Concern when a callback sometimes executes synchronously and asynchronously, which leads to race conditions
  * A `Promise` can only be observed synchronously
* Call the callback too late
  * A `Promise`'s `then` registered observation callbacks are scheduled when `reject` or `resolve` are called
  * These callbacks get invoked at the next asynchronous moment (using the Jobs queue, which basically exists at the end of the event loop to add async events to the tick)
  * When a `Promise` is resolved, all `then` callbacks get executed in order, immediately at the next asynchronous opportunity

```javascript
// This outputs 'A', 'B', 'C'

p.then(function() {
  p.then(function() {
    console.log('C');
  });
  console.log('A');
});

p.then(function() {
  console.log('B');
});
```

* Never calling the callback
  * Nothing can prevent a `Promise` from becoming resolved (or erroring, which is a form of resolution)
  * Even if a `Promise` is locked in an "infinite" delay, this can be handled using a `Promise.race` which can be used to return a rejected `Promise` if a `Promise` takes too long to resolve

```javascript
Promise.race([
  foo(),
  timeout(),
]).then(function(fooData) {
  // foo was resolved in time
}, function(error) {
  // foo failed
  // or timeout occurred (foo did not resolve in time)
});
```

* Calling a callback too few or too many times
  * A `Promise` can only be resolved once
  * This means any individual `them` callback will only be called once

* Swallowing errors and exceptions
  * If at any point in the creation of the `Promise` or the resolution of the `Promise` an error occurs, that error is caught and forces a rejected `Promise` to be returned
  * If an exception occurs in a registered callback (i.e. in the `then`) the `Promise` returned by the `then` will be rejected with the error in question

```javascript
var p = new Promise( function(resolve,reject){
	foo.bar();	// `foo` is not defined, so error!
	resolve( 42 );	// never gets here :(
} );

p.then(
	function fulfilled(){
		// never gets here :(
	},
	function rejected(err){
		// `err` will be a `TypeError` exception object
		// from the `foo.bar()` line.
	}
);
```
