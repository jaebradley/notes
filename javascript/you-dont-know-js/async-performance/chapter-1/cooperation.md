# [Cooperation](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch1.md#cooperation)

* Takes long running processes and batches them up in order to interleave operations into the event loop
* For example, processing a huge Ajax response means that the entire single JavaScript thread is locked into `map`ping (in this case) through the response
* No other Ajax responses, or UI updates, or anything can happen until the processing finishes
* Process these results in async batches, so that other events in the event loop can process

```javascript
var res = [];

// `response(..)` receives array of results from the Ajax call
function response(data) {
	// add onto existing `res` array
	res = res.concat(
		// make a new transformed array with all `data` values doubled
		data.map( function(val){
			return val * 2;
		} )
	);
}

// ajax(..) is some arbitrary Ajax function given by a library
ajax( "http://some.url.1", response );
ajax( "http://some.url.2", response );
```
