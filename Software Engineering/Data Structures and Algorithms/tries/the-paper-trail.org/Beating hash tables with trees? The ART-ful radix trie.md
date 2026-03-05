# [Beating hash tables with trees? The ART-ful radix trie](https://www.the-paper-trail.org/post/art-paper-notes/)

## Trie
* Trie decomposes a key into a sequence of characters, where each character is stored in a separate node
* The possible child nodes for a node is every character in the alphabet
* Checking whether a specific key is in the trie involves starting from the root node
  * Check if the first character in the key is a child node of the root node
  * For this child node, check if the second character is a child of the current node
  * Continue this process until the entire key has been iterated over
* The number of nodes visited is dependent on the length of the key and not on the nodes in the trie
  * There is some crossover point where the number of visited nodes is guaranteed to be smaller than a perfectly balanced binary search tree
  * A perfectly balanced binary search tree has a depth of `log # of keys` 
  * Need to identify the number of keys where `log # of keys > key size / character size`  which is equivalent to `# of keys > 2 ** (key size / character size)` 
  * If keys are 64 bits and each character is 8 bits, then the trie will be shallower than a binary search tree when more than 256 entries need to be stored

## What’s Wrong With Ordinary Tries?
* Most tries are defined like

```python
class TrieNode:
    # There are 256 child entries, for each ASCII character
    children: TrieNode[]
```

* The size of a single character is the "span" of the trie
* The designed `TrieNode` can have a lot of wasted space (assuming each node's `children` array is preallocated with 256 entries) when the number of entries stored in the trie is small

## Adaptive Node Structures
* Instead of nodes having a `children` array that is always allocated with 256 entries, the concept of a node is a union of a node with four children, a node with 16 children, a node with 48 children, and a node with 256 children

```
    union Node {
        Node4* n4;
        Node16* n16;
        Node48* n48;
        Node256* n256;
    }
```

### Node4
* All four children are stored in a list, so looking up a specific matching child involves a linear search
  * However, since the child array is so smile, the impact of the linear search is minimized
* It's possible to design `Node4` to require 36 bytes of memory, so a `Node4` can exist in a single cache line

### Node16
* Similar concept as `Node4` but children are sorted
* Binary search is utilized to find matching children
* SIMD can be utilized to search all keys in parallel

```c
// Find the child in `node` that matches `c` by examining all child nodes, in parallel.
Node* find_child(char c, Node16* node) {
    // key_vec is 16 repeated copies of the searched-for byte, one for every possible position
    // in child_keys that needs to be searched.
    __mm128i key_vec = _mm_set1_epi8(c);

    // Compare all child_keys to 'c' in parallel. Don't worry if some of the keys aren't valid,
    // we'll mask the results to only consider the valid ones below.
    __mm128i results = _mm_cmpeq_epi8(key_vec, node->child_keys);

    // Build a mask to select only the first node->num_children values from the comparison
    // (because the other values are meaningless)
    int mask = (1 << node->num_children) - 1;

    // Change the results of the comparison into a bitfield, masking off any invalid comparisons.
    int bitfield = _mm_movemask_epi8(results) & mask;

    // No match if there are no '1's in the bitfield.
    if (bitfield == 0) return NULL;

    // Find the index of the first '1' in the bitfield by counting the leading zeros.
    int idx = ctz(bitfield);

    return node->child_pointers[idx];
}
```

### Node48
* Child nodes are stored in a 48-element array of pointers (where pointers are 8 bytes)
* There's a 256-element array that maps the 256 8-bit ASCII characters to a pointer in the 48-element array
  * `child_nodes[characters_to_child_node_pointers['f']]` would attempt to look up the child node for the character `f` 
  * Note that a maximum of 48 elements at time in the 256 element array will map to a pointer
* The benefit of using this indirection is that only 48 pointers need to be stored at a time (640 bytes) whereas naively storing 256 pointers would require \~2 kilobytes

## Storing Values
* Basically, the main approach is to add a "leaf node" type where the leaf node has a value pointer
* Given this approach, what's the approach for supporting keys that are prefixes of other keys, like `https://google.com` and `https://google.com/chrome` 
* The two solutions are fixed-length data types or variable-length datatypes + the `NULL` byte
  * If all keys are exactly 64-bytes (i.e. they have a fixed-length) the prefix key problem, by definition, can never occur
  * If keys are variable length, by appending the `NULL` byte to every key
    * The `NULL` byte indicates that this is the end of the key and no characters come after it
    * No string with a `NULL` byte can be a prefix of any other since the string can't have any characters after the `NULL` byte

## Lazy Compression
* If a sequence of single-child nodes ends in a leaf, simply create a single node that has the entire key and its value
  * When this node is encountered during the search process, compare the remaining key to the single node's key character-by-character

## Path Compression
* If a sequence of single-child nodes does *not* end in a leaf, the path is compressed like in lazy compression, but with a single node that has the shared key prefix and then multiple children
* Example: Starting with an empty trie, insert `CAST` and `CASH` 
  * Path compression would create a child node for the root node that has the `CAS` prefix
  * This child node would have two child nodes - one for `T` and one for `H`
