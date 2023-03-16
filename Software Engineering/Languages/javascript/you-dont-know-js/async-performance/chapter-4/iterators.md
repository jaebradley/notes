# [Iterators](https://github.com/getify/You-Dont-Know-JS/blob/master/async%20%26%20performance/ch4.md#producers-and-iterators)

* Function that generates some numbers (using closure)

```javascript
var gimmeSomething = (function(){
	var nextVal;

	return function(){
		if (nextVal === undefined) {
			nextVal = 1;
		}
		else {
			nextVal = (3 * nextVal) + 6;
		}

		return nextVal;
	};
})();

gimmeSomething();		// 1
gimmeSomething();		// 9
gimmeSomething();		// 33
gimmeSomething();		// 105
```

* An iterator is an interface for stepping through a series of values from a producer by calling `next()`

```javascript
// Iterator solution
var something = (function(){
	var nextVal;

	return {
		// needed for `for..of` loops
		[Symbol.iterator]: function(){ return this; },

		// standard iterator interface method
		next: function(){
			if (nextVal === undefined) {
				nextVal = 1;
			}
			else {
				nextVal = (3 * nextVal) + 6;
			}

			return { done:false, value:nextVal };
		}
	};
})();

something.next().value;		// 1
something.next().value;		// 9
something.next().value;		// 33
something.next().value;		// 105
```

* `for...of` loops consume iterators
* Note how the above iterator has `done` always set to `false` - this means that the iterator is never-ending and a `for...of` loop would run infinitely (or until a `break` occurred)
