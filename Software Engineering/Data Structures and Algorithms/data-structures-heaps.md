# [Data Structures: Heaps](https://medium.com/swlh/data-structures-heaps-b039868a521b)

* A heap is a complete binary tree based data structure where nodes at any given level are not sorted in any particular way with respect to each other
  * Because it's a complete binary tree, each level needs to be filled before another level is added
  * Levels are "filled" from left to right
* The height of the tree with `N` nodes is `log(N)`
* Heaps provide access to the greatest / smallest item in a set of items
* Heaps can be represented as an array where node `i`'s parent is located at index `Math.floor((i - 1) / 2)`
  * Left child is at `2 x i + 1` and right child is at `2 x i + 2`
* Insertion involves adding the node at the bottom of the tree (end of the array) and then checking the inserted value against it's parent and swapping the parent with the new node if the parent is greater / less than the new node
  * This process continues until the new node can no longer be swapped ("bubbling up" the new node)
  * Worst-case scenario is that the node makes it all the way to the "root" of the heap so there are `log(N)` swaps (which would mean insertion would be `O(log(N))`)
* Deleting / removing the root element requires the entire tree to shift to "fill" the root node's place
  * Move the last node to the root node
  * And then "bubble down" the node, swapping it with the smaller / larger of it's two children
  * Worst-case is that the entire tree needs to be traversed (like insertion) so the cost is `O(log(N))`
