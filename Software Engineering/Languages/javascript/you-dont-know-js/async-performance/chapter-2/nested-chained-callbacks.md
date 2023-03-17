# [Nested/Chained Callbacks](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch2.md#nestedchained-callbacks)

```javascript
doA( function(){
	doB();

	doC( function(){
		doD();
	} )

	doE();
} );

doF();
```

* Order of operations are
  * `doA`
  * `doF`
  * `doB`
  * `doC`
  * `doE`
  * `doD`

* However, we're making an assumption that `doA` and `doC` are actually asynchronous - if they are actually synchronous, then order of operations is actually
  * `doA`
  * `doB`
  * `doC`
  * `doD`
  * `doE`
  * `doF`

* Even splitting out the hard-coded callbacks into their own functions doesn't account for the actual logical dependencies of callbacks (there *is* a dependency between)
