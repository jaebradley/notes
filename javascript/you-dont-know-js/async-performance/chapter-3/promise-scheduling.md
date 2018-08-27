# [`Promise` Scheduling](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch3.md#promise-scheduling-quirks)

* Note how `p1` is really the resolution of another `Promise` (`p3`)
* What ends up happening is that `p3` gets "unwrapped" into `p1`, which occurs **after** `p2`'s callback is scheduled

```javascript
var p3 = new Promise( function(resolve,reject){
	resolve( "B" );
} );

var p1 = new Promise( function(resolve,reject){
	resolve( p3 );
} );

var p2 = new Promise( function(resolve,reject){
	resolve( "A" );
} );

p1.then( function(v){
	console.log( v );
} );

p2.then( function(v){
	console.log( v );
} );

// A B  <-- not  B A  as you might expect
```
