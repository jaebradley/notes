# Chapter 3. Dictionaries and Sets

## Unpacking Mappings

* `**kwargs` in function calls (all keys must be unique)
  * `my_func(**{'x': 1}, y=2, **{'z': 3})`
  * `{'x': 1, 'y': 2, 'z', 3}`
* `**` can also be used in `dict` literals (multiple times)
 * `{'a': 0, **{'x': 1}, 'y': 2, **{'z': 3, 'x': 4}}`
 * `{'a': 0, 'x': 4, 'y': 2, 'z': 3}`
 * duplicate keys are allowed, keys are overwritten 

## Merging Mappings with `|`

* Creates a new mapping with the union of dict key/values
* Duplicate keys are overwritten
* `|=` updates an existing mapping in-place

## Standard API of Mapping Types

* Implement a custom mapping by extending `collections.UserDict` or wrap a `dict` via composition
* Keys must be hashable (values do not need to be hashable)

### What is Hashable

* An object is hashable if it has a hash code which never changes during its lifetime
* It needs to be compared to other objects
* Hashable objects that are equal to each other must have the same hash code
* Numeric types and flat immutable types `str` and `bytes` are all hashable
* Container types are hashable if they are immutable and all contained objects are also hashable
* A tuple is only hashable if all its items are hashable
* The hash code of an object may be different depending on 
  * Python version
  * Machine architecture
  * Salt added to the hash computation for security reasons
* Hash code is guaranteed to be constant only within one Python process