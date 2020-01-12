# [Insanely Useful JavaScript Array Methods](https://medium.com/javascript-in-plain-english/insanely-useful-array-methods-b3f93bf5415e)

* `new Array` has some unexpected behavior when one argument is passed to the constructor vs. multiple arguments
  * `new Array(5)` will create an array of length `5` where all the values are set to `undefined` while `new Array(5, 6)` will create an array of length `2` where the first value is `5` and the second value is `6`
* `Array.of` has consistent behavior where `Array.of(5)` will create an array with a single value (`5`)
  * `Array.of` also supports passing multiple values
* `Array.from` creates a new array from an array-like / iterable object
  * Often times used to convert an iterable non-Array object (like `HTMLCollection` or `NodeList`) to an actual array
* `Array.fill` modifies all elements of an array from a start index (defaulting to `0`) to an end index (defaulting to array length)
  * `new Array(5).fill('foo')` will create an array with `5` elements set to `foo`
