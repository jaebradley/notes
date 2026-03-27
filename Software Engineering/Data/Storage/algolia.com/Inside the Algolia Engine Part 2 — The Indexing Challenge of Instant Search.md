# [Inside the Algolia Engine Part 2 — The Indexing Challenge of Instant Search](https://www.algolia.com/blog/engineering/inside-the-algolia-engine-part-2-the-indexing-challenge-of-instant-search)

## Generational data structures
* A large data structure containing the older generation of data and a smaller data structure containing the newer generation of data
* Modifying an object involves updating the smaller, newer generation, which should be a faster operation
* Efficiently merging generational data structures involves having these data structures sorted 
  * This allows for parallelized scanning via heap-merge

## Radix trees
* A radix tree is a space-optimized representation of a trie
* Radix trees are sorted
* Algolia attempted to build the complete radix tree on their servers to minimize the amount of a user's on-device memory
* Algolia builds the radix tree in-memory, and then flush the portion of the tree stored in RAM, to disk
* Example dictionary: `fast`, `faster`, `test`, `toaster`
  * Note that the words are sorted
  * Initial tree is a `fast` node that points to a `er` suffix
  * When the word `test` is added to the tree, since there is no shared prefix node, the `fast` + `er` nodes are flushed to disk
  * The `test` node is added to the tree
  * When adding `toaster`, `test` and `toaster` share a `t` prefix and two suffix nodes - an `est` suffix and an `oaster` suffix
* In the worst case scenario, the number of nodes is equal to the longest key in the tree

## Prefix search
* Using example dictionary from above, typing `f` should retrieve `fast` and `faster`
* Calculating the prefix-object for all prefixes takes a lot of time and memory
  * Prefixes can be stored on all radix tree nodes that have children
  * These prefixes can be computed when flushing portions of the tree from RAM to disk
