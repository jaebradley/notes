# Chapter 2. An Array of Sequences

## Overview of Built-In Sequences

* Container sequences can hold items of different types (`list`, `tuple`, `deque`)
  * Holds references to the objects it contains (which could be of any type)
* Flat sequences can hold items of one simple type (`str`, `bytes`, `array`)
  * Stores the value of its contents in its own memory space
* Every Python object in memory has a header with metadata
  * `float` has a value field and two metadata fields
	  * `ob_refcnt` - the object's reference count
	  * `ob_type` - point to the object's type
      * `ob_fval` - `C double` holding the value of the `float`

## Generator Expressions

* `genexp` saves memory because it yields one by one using the iterator protocol instead of building a whole list just to feed another constructor

```python
tuple(ord(symbol) for symbol in symbols)
# (symbol1, symbol2, symbol3, ...)

array.array('I', (ord(symbol) for symbol in symbols))
# array('I', [symbol1, symbol2, symbol3, ...])
# First argument of the array constructor defines the storage type used for the numbers in the array

first_list = [...]
second_list = [...]
for combined in (f'{first_item} and {second_item}' \
    for first_item in first_list \
    for second_item in second_list):
  print(combined)
# generator expression produces one item at a time and the cartesian product of the lists is never built into memory
```

