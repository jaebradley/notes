# Chapter 4. Dictionaries and Sets

## How Do Dictionaries and Sets Work?

### Inserting and Retrieving

* Python appends the key/value data into a standard array, then stores the array index for this key/value in the hash table
  * Reduces memory by 30-95%
  * Can also now keep track of the order in which items were added to the dictionary
* If a key/value to be inserted matches the hash of an existing key/value in the dictionary (and the values don't match) then a new index for this key/value needs to be computed
  * Python's probing mechanism uses the higher-order bits from the calculated hash, giving each hash a different sequence of next possible hashes, reducing future collisions
  * The "lower" bits were used when calculating the initial "key" value (given some appropriate mask, dependent on the number of "buckets" in the hash table)
  * If items have the same X digits, there will not only be a collision, but the sequence of probed indices will be the same
  * Python uses "perturbation" aka additional bits, from the item's hash to identify additional indices
* Similar procedure occurs when looking up a specific key
  * If the key at that specific index matches, the matching value is returned
  * If the key at that index does not match, generate / probe additional indices until the "correct" data is identified, or an empty bucket is found
  * If an empty bucket is found, the data does not exist in the table

### Deletion

* `NULL`s are used as a sentinel value while probing for hash collisions
  * So can't just write `NULL` for that value
* Must write a special value that signifies that the bucket is empty, but there may still be values after it to consider when resolving a hash collision
* In book example, where `Rome` and `Barcelona` have the same hash, 
  * If `Rome` is deleted, subsequent lookups for `Barcelona` need to know that a sentinel value for `Rome` was used and continue to search for `Bareclona`
  * These empty slots can be written to in the future, and are removed when the hash table is resized

### Resizing

* A table that is no more than two-thirds full will have optimal space savings, while still having a good bound on the number of collisions to expect
* A larger table is allocated, the bit mask is adjusted to fit this new table, and all the elements of the old table are reinserted into the new one
* Requires recomputing indices, since the updated mask will result in different indices
* Resizing large hash tables can be expensive
* Default size of a dictionary or set is `8`, so even if you store `3` values, the dictionary will still have `8` slots
* Resizing _only_ happens on insert
* Resizing can also result in a table being scaled down in size

## Dictionaries and Namespaces

* Whenever a variable, function, or module is invoked, there is a hierarchy that determines where to look for these objects
* Python looks inside the `locals` array, which has entries for all local variables
* Then, Python looks inside the `globals` dictionary
* Then, the `__builtin__` object is searched
  * When searching the `__builtin__` object for a given property, doing a dictionary lookup inside its `locals` mapping
* So something like `math.sin` results in
  * Instructions to first load the `math` module
  * Instructions to find the `sin` function within the `math` module
* So doing something like `from math import sin` avoids the lookup for the `math` module when executing the function that uses `sin`
  * And thus, this is slightly faster

