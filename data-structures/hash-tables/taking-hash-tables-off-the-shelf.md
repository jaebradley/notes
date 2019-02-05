# [Taking Hash Tables Off The Shelf](https://medium.com/basecs/taking-hash-tables-off-the-shelf-139cbf4752f0)

* Searching within a linked list or an array has downside of worst-case scenario being iterating through each element in linked list (or at best `log n` in the case of binary search)
* Can use hash table, which combines array and linked lists with hashing function
* Similar to bookshelfs, each index in the array, represents a "slot"
* This "slot" or "shelf" contains a linked list

## Storage

* When items are stored, they are first hashed using the hashing function to determine which index / "shelf" to put them on
* Once this index is identified, the linked list at that index is retrieved and the item is added to the end of the linked list
* Hash collisions will occur (where two items hash to the same value) - simply add item to linked list at hashed index
* Hopefully, the hashing function produces equally-distributed output so that not all items are at the same index (i.e. all books are on one shelf)
  * This will minimize collision, which will in turn produce better performance

## Lookup

* To look up an object, hash the object using the hash function to get index
* Then get linked list at index and iterate through linked list until you find object

## Access Time

* The access time for hash tables is effectively constant time
  * [This SO question is good](https://stackoverflow.com/a/2771398/10039741)
  * Basically, the constant time performance claim assumes that objects can be equality compared in constant time and that there are few hash collisions
  * If, in order to compare objects, every bit of the object must be read, then the performance becomes `O(size of input)`
    * But a hash function might only need to look at a fixed number of bits
  * For a large number of items, performance will rise greater than constant time and it'll become more like `O(n)` - so technically constant time claim is technically false, but approximately true
