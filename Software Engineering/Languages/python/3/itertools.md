# [itertools](https://docs.python.org/3/library/itertools.html)
* `accumulate`
  * Returns a running accumulation
  * Defaults to addition
  * First argument is an iterable, and the second optional argument  is an accumulator
  * Accumulator should accept two arguments the accumulated total and the current iterable value (like `functools.reduce`)
    * Third optional argument is an initial value
* `batched`
  * Batch data from the defined iterable into tuples of length `n` 
  * The last batch may be shorter than `n`
    * If the `strict` argument is true then will raise a `ValueError` 
  * Input is consumed lazily, just enough to fill each batch and the result is yielded as soon as the batch is full or when the input iterable is exhausted
* `chain` 
  * Combines multiple iterables into a single iterator
  * Make an iterator that returns elements from the first iterable until it is exhausted, then proceed to the next iterable, until all iterables are exhausted
  * `chain('ABC', 'DEF')` will return an iterator of `A, B, C, D, E, F` 
* `count`
  * Create an iterator that returns evenly-spaced values beginning with `start` 
  * Takes a `step` argument that defaults to `1`
    * This `step` argument can be a decimal
* `groupby`
  * Takes an `iterable`  argument and an optioanl `key` argument
  * If the `key` argument is not specified / `None` , `key` defaults to an identity function
  * It generates a break or new group every time the value of the key function changes
    * Usually means that the iterable needs to be sorted
  * When `groupby` is advanced, the previous group is no longer visible, so if the returned data is needed later, it should be stored as a list
  * `[k for k, g in groupby('AAAABBBCCDAABBB')] → A B C D A B`
  * `[list(g) for k, g in groupby('AAAABBBCCD')] → AAAA BBB CC D`
* `islace`
  * Creates an iterator from the input iterable that takes `start` , `stop`, and `step`  arguments
* `starmap` 
  * Like `map` when the iterable already has elements that can be applied to the mapping function
  * While `map` 's function might have the form `function(a, b)` , `starmap` 's function has the form `function(*c)`
  * `starmap(pow, [(2,5), (3,2), (10,3)]) → 32 9 1000`
