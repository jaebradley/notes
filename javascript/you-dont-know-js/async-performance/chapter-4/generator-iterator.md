# [Generator Iterator](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md#generator-iterator)

* Can use a generator to produce the number producer

```javascript
function *something() {
	var nextVal;

	while (true) {
		if (nextVal === undefined) {
			nextVal = 1;
		}
		else {
			nextVal = (3 * nextVal) + 6;
		}

		yield nextVal;
	}
}
```

* Can `for (var v of something())` to loop through generator
  * Notice how we have to call `something` in order to loop through it - `something` by itself is just a generator function
  * Calling `something` returns an `iterator`
  * Since can `for...of` through `something` it's also an `iterable`
    * Generator's iterator has `Symbol.iterator` function which basically `return`s `this`
