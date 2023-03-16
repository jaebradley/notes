# [Async Generators](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md#iterating-generators-asynchronously)

* Making ajax request using generators

```javascript
function foo(x,y) {
	ajax(
		"http://some.url.1/?x=" + x + "&y=" + y,
		function(err,data){
			if (err) {
				// throw an error into `*main()`
				it.throw( err );
			}
			else {
				// resume `*main()` with received `data`
				it.next( data );
			}
		}
	);
}

function *main() {
	try {
		var text = yield foo( 11, 31 );
		console.log( text );
	}
	catch (err) {
		console.error( err );
	}
}

var it = main();

// start it all up!
it.next();
```

* `main` blocks on `yield` - this `yield` will assign result of `foo` (aka asynchronous ajax request) to `text`
* Notice how `foo` calls `next` on the iterator with the response data (when successful)
  * And conversely, it calls `throw` on the iterator when unsuccessful
* This means that the paused `yield` expression receives the response data and restarts the generator code
