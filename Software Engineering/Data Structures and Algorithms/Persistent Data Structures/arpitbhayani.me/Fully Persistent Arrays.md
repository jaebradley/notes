# [Fully Persistent Arrays](https://arpitbhayani.me/blogs/fully-persistent-arrays)
* Fully persistent data structures allow access and modification to all historical versions
  * They do not restrict modification
  * Revisit any historical version of the data structure, modifying it like forking a new branch
* The most interesting array operation is the `update` operation, which takes a parent array, an index, and a value
  * Returns a new array that is identical to the input array, except for the element at the specified position
  * The parent array remains unaffected
* Instead of storing all versions separately, Backer's trick allows computation of any array version by replaying historical changes

## Tree of modifications
* Tree has all versions of the array, storing only the modifications made to elements
* Each version is derived from a parent version
* Root of tree references the initial version of the array
* Each tree node has a reference to a parent value, the modified index, and the updated value
* So changing the first element of the root array from `7` to `8` would create a node that looked something like `Node(parent=root, value=8, index=0)`

<img width="1200" height="628" alt="image" src="https://github.com/user-attachments/assets/c10c09e9-f77a-487e-9b16-2eae6b668dfc" />

* Getting the current value of an array that has been modified requires traversing the tree of modification
  * Need the version of the array from which an element at the specified index needs to be fetched
  * `O(n)` in the worst case

## Rerooting
* Reads are expensive and the initial array does not have to be the root forever
  * If all reads for for a specific version of the array, reroot so that version of the array is the root version
* Reroot the entire tree so that any child node can become the root
  * A sequence of tree rotations to make any desired array version the root

<img width="1200" height="628" alt="image" src="https://github.com/user-attachments/assets/17af6ade-e602-4897-b75b-ab6abba87c0c" />
