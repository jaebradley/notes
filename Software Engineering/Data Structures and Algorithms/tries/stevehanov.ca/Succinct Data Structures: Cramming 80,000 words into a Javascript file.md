# [Succinct Data Structures: Cramming 80,000 words into a Javascript file](https://stevehanov.ca/blog/succinct-data-structures-cramming-80000-words-into-a-javascript-file)
* JSON is a useful data format, but it is not compact, and there is some bandwidth that is wasted when downloading the JSON data to the browser
* Tries are also memory hungry
* Succinct data structures encode data efficiently so that they do not need to be decoded to be used
* There is a `rank(position)` method that returns the number of `1` bits up to, and including, the specified position
* There is a `select(n)` method that returns the position of the `nth` `1` bit
  * `select` is the inverse of `rank`
  * If the `nth` `1` bit is found at position `10`, then the number of `1` bits at position `10` is `n`
* Rank function is implemented in constant time using a lookup table
* Select function is usually implemented in `O(log n)` time by performing binary search using the rank function

## A Succinct Trie
* There's a "super root" that is an additional sentinel node whose sole child is the root of the trie
  * This is to help make the positional math easier
* Nodes are processed, and encoded, in level order
* Nodes are encoded with a `1` for each child, and then this encoding is terminated with a `0`
* A node with five children is encoded `111110`
* A leaf node is encoded `0`
