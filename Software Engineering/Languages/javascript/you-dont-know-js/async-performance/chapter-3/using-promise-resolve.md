# [Using `Promise.resolve`](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch3.md#trustable-promise)

* `Promise.resolve` takes any `then`able and returns a "real" `Promise`
* If what is passed to `Promise.resolve` is already a genuine `Promise`, you just get it right back
* If unsure if `foo` returns a `Promise`, can just do

```javascript
// don't just do this:
foo( 42 )
.then( function(v){
	console.log( v );
} );

// instead, do this:
Promise.resolve( foo( 42 ) )
.then( function(v){
	console.log( v );
} );
```

* `Promise.resolve` will also normalize function behavior
  * If a function sometimes returns a `Promise` sometimes and not a `Promise` other times, wrapping it's return value in a `Promise.resolve` will always guarantee that the output is a `Promise`
