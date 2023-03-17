# [Distinct Uses of JavaScript Array Slice](https://codeburst.io/js-by-example-8-distinct-uses-of-javascript-array-slice-4e4e95a470e4)

* [`Array.prototype.slice`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice) returns a shallow copy of an array
* It takes up to two arguments
  * The first argument represents a starting index
  * The second argument represents an ending index (non-inclusive)
* Using negative indices indicates counting back from end of array
* `.slice()` by itself will shallow copy an array
* `.slice(N)` will shallow copy an array starting from `N`
* `.slice(-N)` will shallow copy the last `N` elements of an array
* `.slice(0. N)` will return the first `N` elements of an array
* `.slice(M, M + N)` will return a subarray of length `N` from index `M`
* Array-likes (like `arguments` or `NodeLists`) can be converted to arrays using `slice`
  * `Array.prototype.slice.call(arguments)`
  * Array-likes are objects that have a `length` and can be accessed via numerical indices
* `[ ...values.slice(0, index), newValue, ...values.slice(index + 1)]` - modify a particular index in an array and return this new modified array
