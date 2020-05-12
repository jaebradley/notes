# [Merging Dictionaries In Python 3.9](https://towardsdatascience.com/merging-dictionaries-in-python-3-9-3b0409aa91a8)

* Clumsy way to merge two dictionaries
  * Two `for` loops - first one iterates over the items in the first dictionary and adds it to the new dictionary
  * Second `for` loop does the same, but over the second dictionary
* Using the `update` method
  * Because `update` modifies dictionary in-place, need to first copy first dictionary, then call `update` on the copied dictionary with the second dictionary
* Using dictionary unpacking
  * From Python `3.5`, can create a merged dictionary doing `d3 = {**d1, **d2}`
  * Can also do `d3 = dict(d1, **d2)`
* In Python `3.9` can use the union operator (`|`)
  * Union operator was used for merging sets (`{1, 2, 3} | {3, 4, 5}` => `{1, 2, 3, 4, 5}`)
  * Union operator can also be used as part of augmented assignment - `d1 |= d2` => `d1 = d1 | d2`
  * Because dictionaries are ordered by insertion order, `d1 | d2` is not necessarily equivalent to `d2 | d1`