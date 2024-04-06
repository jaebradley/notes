# Chapter 1. The Python Data Model

* Use `namedtuple` to build classes of objects that are bundles of attributes with no custom methods (like DB records)
* `random.choice` is a method to pick a random item from a sequence
* If a collection has no `__contains__` method, the `in` operator does a sequential scan

## How Special Methods Are Used

* Meant to be called by the Python interpreter and not by you
* `len(my_object)` -> Python calls the `__len__` method
* For built-in types (`list`, `str`, `bytearray`) written in C, these objects have a struct called `PyVarObject`
  * `ob_size` field on struct that holds the collection's item count
  * Much faster than calling a method

## String Representation

* The `__repr__` special method is called by the `repr` built-in
  * It gets the string representation of an object for inspection
  * Without a custom `__repr__`, Python console would display objects like `<Vector object at 0x10e1000070>`
* Uing the `!r` f-string conversion string is good practice
  * Differentiate between `Vector(1, 2)` vs. `Vector('1', '2')` when constructor's takes numbers not strings
* If possible, should match source code necessary to re-create the represented object
* __str__` calls `__repr__` as a fallback

## Boolean Value of a Custom Type

* To determine if a value is truthy or falsy, Python applies the `bool` function
* By default, instances of user-defined classes are considered truthy (unless `__bool__` or `__len__` is implemented)
* `bool(x)` -> `x.__bool__()`
* If `__bool__` is not implemented, `x.__len__()` is called
  * If `__len__` returns zero, `bool` returns `False`, otherwise `bool` returns `True`

## Collection API

* There are three essential interfaces that every collection should implement
  * `Iterable` to support unpacking and other forms of iteration
  * `Sized` to support the `len` built-in function
	* `Container` to support the `in` operator
  * Python does not require concrete classes to inherit from these interfaces - for example, any class that implements `__len__` satisfies the `Sized` interface
* `Sequence` is `Reversible` as they support arbitrary ordering of their contents
  *  mappings and sets do not
	* Since `3.7`, `dict`s are key insertion ordered, but this does not mean you can rearrange the keys in a `dict` arbitrarily

