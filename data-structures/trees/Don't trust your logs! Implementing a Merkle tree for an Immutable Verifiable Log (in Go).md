# [Don't trust your logs! Implementing a Merkle tree for an Immutable Verifiable Log (in Go)](https://aly.arriqaaq.com/merkle-tree-and-verifiable-data-structures/)

* Tamper-evident logs can be cryptographically proven that the data hasn't been unexpectedly changed
* Merkle tree is a binary three that stores values at the lowest level of the tree and uses cryptographic hash functions
  * Leaves compute the has of their own attributes, parents derive the has of their children's hashes concatenated left-to-right
  * The hash at a particular subtree is recursively dependent on all of its descendants
* Merkle trees van prove values are present by constructing efficient membership proofs
  * Each membership proof includes a Merkle audit path, and it is verified by recomputing all hashes, bottom-up
  * The proof is "valid" if the recomputed root hash matches that of the original Merkle tree
* Merkle audit path for a leaf is the list of all nodes in the Merkle Tree required to compute the Merkle Tree Hash for that tree
* The tree can be represented as a 2D array of bytes
* Input to the Merkle Tree Hash is a list of data entries
  * These data entires area hashed to form the leaves of the Merkle Hash Tree (i.e. ordered list of `n` inputs, `D[n] = {d(0), d(1),..., d(n - 1)}`
  * Output is a single 32-byte Merkle Tree Hash
  * Hash function is SHA-256
* The Merkle Tree Hash of an n-element list is then recursively defined as
  * `SHA-256(0x01 + Merkle Tree Hash(Input List[0th element to kth - 1 element]) + Merkle Tree Hash(Input List[kth element to nth element])` (`+` represents concatenation)
  * `k` is the largest power of `2` that is is less than `n`
* Merkle audit path for a leaf in a MTH is the shortest list of additional nodes in the Merkle Tree required to compute the Merkle Tree Hash for that tree
  * Each node in the tree is either a leaf node or is computed from the two nodes immediately below it
  * The audit path is basically the path from the leaf node to the root + the MTH of the other side of the tree
  * If the root value computed from the audit path matches the MTH root value, the aduti path is proof that the leaf exists in the tree
  * For a `k` such that `k` is the largest power of two smaller than the length of the input list
  * For an `m` that represents the index of the leaf value in the input list
  * Given a existing Merkle Tree, the audit path is defined recursively as
    * `Path(m, input list) = Path(m, list[0th element to k - 1th element]) + MTH(list[kth to nth element])` for `m < k` (left-side of tree)
    * `Path(m, input list) = Path(m - k, list[kth element to nth element]) + MTH(list[0th to k-1th element])` for `m >= k` (right-side of tree)
