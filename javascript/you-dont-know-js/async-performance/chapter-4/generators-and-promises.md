# [Generators and Promises](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md#generators--promises)

* How should a promise get hooked up to a generator?
  * Generator waits for promise to resolve, and then resume the generator with the fulfilled value or throw an error if the promise was rejected

```javascript
function foo(x,y) {
	return request(
		"http://some.url.1/?x=" + x + "&y=" + y
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
```

* However, need to adjust the implementation code to wait for `Promise` to resolve

```javascript
var it = main();

var p = it.next().value;

// wait for the `p` promise to resolve
p.then(
	function(text){
		it.next( text );
	},
	function(err){
		it.throw( err );
	}
);
```

* However, this approach does not scale if there are multiple steps with `Promise`s

## `async` / `await`

* Instead of the previous `Promise`-wrapping logic, can use `async` / `await` `ES7` syntax
* Note how `main` is not a generator function, but instead an `async` function

```javascript
function foo(x,y) {
	return request(
		"http://some.url.1/?x=" + x + "&y=" + y
	);
}

async function main() {
	try {
		var text = await foo( 11, 31 );
		console.log( text );
	}
	catch (err) {
		console.error( err );
	}
}

main();
```
