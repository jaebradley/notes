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

## Tuples Are Not Just Immutable Lists

* Used as records with no field names
* `%` formatting operator understands tuples and treats each item as a separate field
* `for` loop knows how to retrieve items of each tuple separately (unpacking)
* `_` conventional dummy variable name
	* In a `match/case` `_` is a wildcard that matches any value
	* `_` is the result of the previous command in the Python console (as long as the result is non-`None`)

## Tuples as Immutable Lists

* tuple will never change its length
* Uses less memory than a list of the same length
  * `tuple` instance is allocated the exact memory space it needs
  * `list` instances on the other hand, are allocated with room to spare, to amortize the cost of future appends
	* Tuple items are stored in an array in the tuple struct
		* `list` holds a pointer to an array of references stored elsewhere
		* When list grows beyond currently allocated space, Python reallocates the array of references to make room
		* The extra indirection makes CPU caches less effective
* References in a tuple cannot be deleted or replaced, but if the reference points ot a mutable object and the object changes, then the value of the tuple will change
* An object is only hashable if its value can never change

## Unpacking Sequences and Iterables

* Requires that the iterable yields exactly one item per variable in the receiving end, unless you use a `*` to capture excess items
* Swap values of variables without temporary variables using unpacking
  * `b, a = a, b`

```python
a, b, *rest = range(5)
# a = 0, b = 1, rest = [2, 3, 4]

a, b, *rest = range(3)
# a = 0, b = 1, rest = [2]

a, b, *rest = range(2)
# a = 0, b = 1, rest = []
```

## Nested Unpacking

```python
# data structure looks something like
# list(tuple((str, str, float, tuple(float, float)))

for name, _, _, (latitutde, longitude) in data:
  # process
```

* Note how the latitude and longitude values in the last element tuple were unpacked

## Pattern Matching with Sequences

```python
match message:
  case ['FIRST', field2, field3]:
    self.process_first_case()
	case ['SECOND', field4]:
		self.process_second_case()
	case ['THIRD', field5, field6]:
		self.process_third_case()
  case ['THIRD', field7, field8, field9]:
  	self.process_fourth_case()
	case _:
		raise InvalidCommand(message)
```

* `message` is the _subject_ `match/case` and the value that Python will try to match to the patterns in each `case` clause
* Explanation of first pattern
	* Match subject with a sequence of three items
	* First item must be the string `BEEPER`
	* Second and third item can be anything, and bound to variables `field2` and `field3`
* Explanation of second pattern
  * Matches any subject with a sequence of two items
	* First item must be the string `NECK`
* Note that the third and fourth case have the same requirement for the value of the first item
	* They differ in the required number of fields
* Last `case` statement is the default case statement and will match any subject that did not match a previous pattern

```python
case [name, _, _, (latitude, longitude)] if longitude <= 0:
# note the additional "guard" via the if statement
# guard is only evaluated on pattern match
```

* Explanation of the above sequence pattern
	* Matching sequence must have four items
	* Last item must be a two-item sequence
* Instances of `str`, `bytes`, and `bytearray` are not handled as sequences in the context of `match/case`
	* `match` subject is treated as an "atomic" value (`987` is treated as a single integer value vs. a sequence of digits)
* `_` matches any single item in that position, and is never bound to the value of the matched item
  * Only variable that can appear more than once in a pattern

```python
# can also set variables in a pattern like
case [name, _, _, (latitude, longitude) as coord]:
# the above sets coord as a variable

# can make more specific patterns like
case [str(name), _, _, (float(latitude), float(longitude))]:
# this pattern states that it 
# 1. will match 4 item sequences
# 2. first item must be a string (and be assigned to the variable "name")
# 3. fourth item must be a two element sequence
# 4. two-element sequence is comprised of a float first element (assigned to the variable name "latitude")
# 5. second element is a float assigned to the variable name "longitude"
```

* `*_` matches any number of items, without binding them to a variable
	* `*extra` would bind items to the variable `extra` as a list with `0+` elements
* Above `case` statement could be re-written like `case [str(name), *_, (float(latitude), float(longitude))]`

## Slice Objects

* `v[a:b:c]` can be used to specify a stride/step (`c`) resulting in skipped items
  * A negative stride returns items in reverse

```python
v = 'bicycle'
s[::3] 
# starting from index 0, select the element, then skip 2 elements, and continue until the end of the string
# bye

s[::-1]
# starting from index 0, select the element, then skip 0 element, and continue until the end of the string. then reverse the results.
# elcycib
```

```python
# Example of some named slices that might increase readability / intent
UNIT_PRICE = slide(40,52)
DESCRIPTION = slice(6, 40)

for item in items:
	print(item[UNIT_PRICE], item[DESCRIPTION])
```




















