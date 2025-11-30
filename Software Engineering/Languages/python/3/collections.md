# [collections](https://docs.python.org/3/library/collections.html#collections.ChainMap)
* `Counter` : a `dict` subclass that counts hashable objects
  * Counts are stored as values in the dictionary
  * Returns a count of `0` if a key does not exist in the `Counter`  object
  * Setting a count to `0` does not remove the element from the counter (use `del`)
  * As of Python 3.7+, `Counter` remembers insertion order
  * `elements()` : returns an iterator over the counted elements repeating as many times as its count
    * Returned in encounter order
  * `most_common`: returns a list of elements, from most to least common, taking a count value as an optional argument
    * When this count is omitted, all elements are returned
    * Ties are broken by encounter order
  * `total`: returns sum of all counts
* `deque`: double-ended queue
  * If a `maxlen` is not specified / `None`, then the `deque` can grow to an arbitrary length
    * Once a bounded-length deque is full, when new items are added, a corresponding number of items are discarded from the opposite end
  * `rotate(steps=1)`: rotates the `deque` the specified number of steps to the right (when positive) and the specified number of steps to the left when negative
* `defaultdict`: creates dictionaries that call the specified `default_factory` argument when a key is encountered for the first time
  * So `defaultdict(list)` will call the `list` function when a key is encountered for the first time
  * Simpler and faster than equivalent using `dict.setdefault()`
