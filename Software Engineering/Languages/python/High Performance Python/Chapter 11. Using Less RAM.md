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
* Sets of `4` bytes are added each time the size of the number crosses the previous limit
