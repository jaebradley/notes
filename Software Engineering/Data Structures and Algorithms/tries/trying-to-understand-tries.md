# [Trying to Understand Tries](https://medium.com/basecs/trying-to-understand-tries-3ec6bede0014)

![trie-example](https://cdn-images-1.medium.com/max/600/1*-KWorUiWCwn-a5iGw2chZg.jpeg)

* Each trie has an empty root node, with links / references to other nodes
* The number of child nodes in a tried depends completely upon the total number of values possible
  * English alphabet has 26 letters vs. Khmer alphabet has 74 characters
* Each node in a trie has
  * a value, which could be `null`
  * an array of references to child nodes, all of which might also be `null`

![trie-traversal](https://cdn-images-1.medium.com/max/600/1*sZOrNXzlQICVv5ePpav1-g.jpeg)

* To add a word to a trie
  * check to see if word doesn't already exist
  * traverse branch where word would live and insert values into node's reference where word should go
* Empty trie walk-through
  * Empty root node, with value of `""`
    * array of `26` references, all of which are `null`
  * Insert `pie` and give it a value of `5`
    * Look for pointer for `p`
    * Since trie is empty, the reference for `p` will be empty
    * Create new node, assign it value of `p`
    * root should now reference `p` node at index `15`
    * Create new `i` node and link it with `p`
    * Create new `e` node and link it with `i`
      * Assign `e` node value of `5`
* If search for `pi`
  * Since `i` node currently has `null` value, `pi` doesn't exist in tree
    * This is a "search miss"
* Let's say add `pies` and now delete `pies`
  * Traverse down to `s` in `pies` and reset value to `null`
  * Check `pies`' pointers and make sure they are empty
* Tries take up a lot of memory and space with empty pointers
  * Remember, for each node that is added, an entire array containing 26 `null` pointers would have to be initialized as well
* Creating a trie is worst-case `O(mn)`
  * `m` is longest key in trie
  * `n` is number of keys in trie
* Searching a trie is worst-case `O(an)`
  * `a` is length of word
  * `n` is number of total words
