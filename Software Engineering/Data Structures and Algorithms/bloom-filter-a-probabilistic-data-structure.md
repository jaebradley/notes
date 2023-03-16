# [Bloom Filter: A Probabilistic Data Structure](https://medium.com/system-design-blog/bloom-filter-a-probabilistic-data-structure-12e4e5cf0638)

* Membership lookup in constant space & time - is this item in the set?
  * Infrequent false positives (says that item is in set when it isn't)
  * No false negatives (will never say item isn't in set, if it is)
* Constant time complexity for adding items and asking if key is present or not
* Cannot remove item from bloom filter
* Hash function takes input data, of any length and returns an output of smaller, fixed length
  * Mostly are one-way operations (i.e. produce identifier from the data and not vice-versa)
  * Same input must always have same output
  * Output values are uniformly distributed - each of the possible output values should be equally likely
  * Output should be distributed randomly - similar input should not produce similar output
  * Output should minimize collisions - provide unique output, if possible
* Bloom filter uses a bit array of size `m` - all bits are set to 0
* Adding an element means hashing the element `k` times using a set of `k` hash functions and setting bit membership for the resulting index
  * The index is produced by taking the modulo of the hash result of the element and the size of the array (`m`)
* Testing for membership involves going through the same process as adding an element and asserting that each index is set
  * If any index is not set, then element is _not_ in the set
  * If all indices are set, then element _may_ be in the set

```bash
// Initial bit vector
[0, 0, 0, 0, 0, 0, 0, 0, 0, 0]

// SYSTEM is added to filter - hash values are 4, 5, and 9
// Set related bits to 1
[0, 0, 0, 0, 1, 1, 0, 0, 0, 1]

// DESIGN is added to filter - hash values are 1, 6, and 9
// Note the collision for index 9
[0, 1, 0, 0, 1, 1, 1, 0, 0, 1]
```

* Let's say `JAE` hashes to 4, 6, and 9
  * Never added `JAE` to bloom filter, but all bits are set - this is example of false positive
* Let's say `BAEBAE` hashest to 1, 3, and 9
  * Since 3 is not set, can say that `BAEBAE` was never added to filter
* Removing `DESIGN` means that unset 1, 6, and 9
  * But `SYSTEM` also uses index 9
  * This means that must remove `SYSTEM`
  * Impossible to remove an element from a bloom filter without removing other elements
* If bit array size is too small, all bits will be set to 1 and many false positives
* Too few hash functions, greater chance of false positives
* Too many hash functions and bloom filter becomes slow (`test` operation is `O(k)`)

