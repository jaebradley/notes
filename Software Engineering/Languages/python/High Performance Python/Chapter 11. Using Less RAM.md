# Chapter 11. Using Less RAM
* Generally speaking, using less RAM generally leads to your data getting consumed faster
  * it will move around buses faster, more of it will into caches

## Objects for Primitives are Expensive
* Allocating a temporary list of 100 million unique numbers consumes ~3.8 GB
* Memory can be cached in the running process so it's always safer to exit and restart the Python shell when using `memit` for profiling

## The `array` Module Stores Many Primitive Objects Cheaply
* `array` module efficiently stores primitives like integers, floats, and chars by creating a contiguous block of RAM to hold the underlying data
* Allocating 100 million unique integers in a contiguous chunk of memory using `array` consumes ~760 MB - far less than the previous list of integers approach
* The numbers in the `array` are not Python objects, they are bytes
  * Dereferencing those bytes would result in a Python `int` object
  * So if they will be used for immediate computation, no overall savings will occur
  * Savings will occur if the array is passed to an external process or if only a subset of the data is used

## Understanding the RAM Used in a Collection
* An `int` in Python is a variable-sized object of arbitrary size
  * Will be greater than an `8`-byte C integer
* A basic object costs `24` bytes in Python 3.7
  * `sys.getsizeof(0)` outputs `24`
  * An empty byte sequence costs 33 bytes
* Sets of `4` bytes are added each time the size of the number crosses the previous limit
* `sys.getsizeof([])` only calculates the cost of the list itself and _not_ the cost of the contents of the list
  * Empty list is 64 bytes and each item in the list allocates another 8 bytes
* Author prefers to use `memit`

## Probabilistic Data Structures
* Trade accuracy for decreases in memory usage
  * HyperLogLog++ structure using 2.5 KB can count ~8 billion unique items with ~1.6% error
    * Given ~41 KB of memory, and the error rate drops to 0.4%
    * Comparatively, storing this data in a set (let's say it's license plates) would consume ~4 GB in memory

### Very Approximate Counting with a 1-Byte Morris Counter
* Keep track of an exponent that starts at `0`
* The approximated value of the counter is calculated using `2 ^ exponent`
* This provides an order of magnitude estimate
* Each time we ask the counter to increment itself the following "test" occurs
  * The exponent is incremented when `random.uniform(0, 1) <= 1 / (2 ^ exponent)`
* As the exponent gets larger, the probability of incrementing decreases drastically
* Error relative to the actual count is large, but only 1 byte is used

### K-Minimum Values
* If we keep the k smallest unique hash values we've seen, we can approximate the overall spacing between the hash values and infer the total number of items
  * Takes advantage of the fact that hash functions should uniformly distribute their output values

### Bloom Filters
* Work by having multiple hash values represent an input value
* If we see an input value with the same set of values, we can be reasonably confident that we've seen the same input value
* Can imagine a list of booleans
  * When an input value returns a collection of hash values, like `10, 4, 7`, the list's 10th, 4th, and 7th indices would be set to `True`
  * To check if an item has been added to the filter before, calculate its hash values, and see if all the hash value indices are set to `True`
* If the filter says we have not seen an item before, we can be 100% certain we have not seen the item
* If the filter says we have seen an item before, there is a chance we have not seen it and the indices are set to `True` due to hash collisions
  * For example, item 1 sets the 10th and 4th indices to `True` and item 2 sets the 7th and 8th indices to `True`
  * We'd think that an item 3, which hash never been added, that maps to the 10th, 4th, and 7th indices has been seen, because all of its indices are `True`
If we insert many more items than the filter has capacity for, the most extreme case is that the array of booleans indicating whether a hash value for that index has been seen will all be `True` and thus we say we have seen every item
* Bloom filters are sensitive to their initial capacity
* Can only take the union of two Bloom filters with the same capacity and error rate
* Need to be wary about getting a new unioned Bloom filter that is over capacity and not reliable

### Scalable Bloom Filters
* Chain together multiple Bloom filters
* Add a new Bloom filter when more capacity is needed
* Iterate over all sub-Bloom filters until the object is found or the list of filters is exhausted
* In example implementation, each new Bloom filter’s error rate is the previous Bloom filter’s error rate x (the previous Bloom filter’s error tightening ratio (which looks to be the same) ^ # of Bloom Filters)

### Timing Bloom Filters
* Elements are expired out of the data structure

### Cuckoo filter
* Has better space efficiencies than a traditional Bloom filter
* When a fixed number of objects need to be kept
* Performance degrades when load limit is reached and there are no options for automatic scaling

