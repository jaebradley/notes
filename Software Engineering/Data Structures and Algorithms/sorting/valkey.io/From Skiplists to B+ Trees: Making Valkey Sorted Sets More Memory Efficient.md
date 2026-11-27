# [From Skiplists to B+ Trees: Making Valkey Sorted Sets More Memory Efficient](https://valkey.io/blog/from-skiplist-to-b+tree/)
* Large sorted sets are backed by a skiplist paired with a hashtable
* Hashtable gives `O(1)` lookup times while the skiplists give `O(log n)` ordered lookup times by rank/score
* Skiplist stores one element per node, scattering memory that are all connected by pointers

## Memory Overhead
* Each Valkey skiplist node has a backward pointer to the previous node (`8 bytes`)
* Additionally, on average, each node holds 1.33 levels of forward pointers and values
  * Each skiplist node has a 25% of holding another level
  * Each level costs `8 bytes` of memory for the pointer + another `8 bytes` for the span value
* Total is ~`37 bytes` per node before accounting for the node's value

## Cache misses and dependent fetches
* L1 cache hit takes ~`1 ns` vs. DRAM access can take ~`100ns`
* Each skiplist node is a separate allocation, with memory scattered across the heap
  * This means iterating over the nodes in a skiplist can repeatedly fetching data from unrelated memory locations
  * Traditional skiplists experience 2.5-5x more cache misses than comparable B-trees

## What Replaced It: `fbtree`
* B+ tree keeps entries sorted in fixed-size nodes
* Leaf nodes hold the entry data
* Intermediate nodes hold pointers to other intermediate nodes or leaf nodes
* `fbtree` (Feature B+ Tree) nodes can hold up to 61 entries / child pointers
  * In comparison, a traditional skiplist's 25%-per level rule gives it an effective branching factor of ~4
  * Both structures retain `O(log n)` search complexity, but a B+ tree has substantially fewer levels to traverse and thus fewer opportunities for expensive memory fetches
* A modern CPU fetches adjacent cache lines efficiently when data is stored contiguously

### Optimizations
* In a B+ tree keyed by variable-length strings, picking a child node means comparing the lookup value against a child node's anchor value
  * A child node's anchor is the largest member in that child node
* `fbtree` nodes store the anchor value, along with a `4 byte` "feature" value
* Because anchor values often share a common prefix, `fbtree` stores that shared prefix separately and use the four feature bytes to distinguish children
* These feature bytes can then be compared in parallel using SIMD
* If the feature bytes do not uniquely identify a child, the range needs to be narrowed using full binary search