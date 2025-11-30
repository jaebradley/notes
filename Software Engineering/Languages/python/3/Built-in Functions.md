# [Built-in Functions](https://docs.python.org/3/library/functions.html)
* `bin` : converts an integer number into a binary string prefixed with `0b` 
  * Result is a valid Python expression
* `chr`: returns the string representation of the specified Unicode code point
  * `chr(97) -> "a"` 
* `enumerate` : takes an iterable and returns a tuple where the first value is the iteration count and the current iterated value
  * Optional `start` argument that defaults to `0` 
* `ord`: returns the Unicode code point of the specified single-character string
* `zip`: Iterate over multiple iterables in parallel, producing tuples
  * The `ith` tuple contains the `ith` element from each of the argument iterables
  * By default, `zip` stops when the shortest iterable is exhausted, ignoring the remaining elements int he other iterables
    * `strict=True` will raise a `ValueError` if one of the iterables is exhausted before the others
