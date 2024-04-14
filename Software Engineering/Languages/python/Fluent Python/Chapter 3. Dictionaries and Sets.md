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
