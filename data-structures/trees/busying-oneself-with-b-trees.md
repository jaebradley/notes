# [Busying Oneself With B-Trees](https://medium.com/basecs/busying-oneself-with-b-trees-78bbf10522e7)

* Special case of a self-balancing binary tree
* A key property of B-trees is that the tree nodes can contain data in the form of multiple `key`s
  * These keys are used to order the child nodes
  * These keys are in sorted order
    * This helps determine if move down left, right, or middle of tree
* Another key property of B-trees is that a node can have more than two children
* If there are `n` keys in a node, then that node will always have `n+1` children
* All of the leaves in a B-tree must be at the same depth, always
* Because the keys are sorted and the ability to sequentially traverse through the tree, B-trees are effective for storing data in an indexed database
  * Databases can read off sections of disk at a time

## Example Using 2-3 Tree

* The non-root and non-leaf nodes of a 2-3 tree must contain either one key and a reference to two child nodes
  * Or two keys and a reference to three child nodes
* Leaf nodes can contain one or two keys

      [8]
     /   \
  [3,5]  [11]
 /      /    \
[1,2] [9,10] [12]

* Keys are sorted as in-order traversal (left-root-right)

## Branching Factor

* The branching factor determines number of keys, and number of children
* In the `2-3` case, the branching factor was `2`
* Generalized, the number of children per node in a B-tree is always between the branching factor and 2 times the branching factor, including the branching factor itself
  * Branching Factor <= Children < 2 * Branching Factor
* Keys in node is possible number of children, minus 1
* Root node can have as few keys as it'd like

## Insertion

Inserting `6` into

      [15,29]
     /   |    \
[4,8] [22,25] [31,36]

* End up with overflowed node `[4,6,8]`, which can be split (i.e. take middle element from overlowed node and bring up to correct place in parent)
  * Keep splitting up to parent if continually overflowed

## Removal

      [9,20]
     /   |   \
[2,7]  [13]  [24,28]

* Removing `13` involves rotating a key from the parent node down
* Need to fill rotated key since parent node must have 2 keys if it has 3 children
* In rotation, take nearest in-order sibling element and replace the empty key in the parent node with that key
* So `9` gets rotated down to take `13`'s spot, and then `7` gets moved up to take `9`'s spot
