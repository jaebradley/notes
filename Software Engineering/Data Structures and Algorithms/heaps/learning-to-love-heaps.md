# [Learning To Love Heaps](https://medium.com/basecs/learning-to-love-heaps-cef2b273a238)

* A binary tree with two special properties - it must have all of its nodes in a specific order, and its shape must be complete
  * "complete" means that every single level of the tree must be completely filled, except for the last level
  * "complete" also means that the last level of the tree must always have the left-most nodes filled first
  * All parent nodes (including root) must be greater than or equal to or less than or equal to the value of its children nodes.
    * For example, `min heap` is a heap where every single parent node, including the root, is less than or equal to the value of its children nodes. `root` will always have smallest value.
    * `max heap` is a heap where every single parent node, including the root, is greater than or equal to the value of its children nodes. `root` will always have largest value.
* Heap is not restricted by the rules of a binary search tree - no restrictions on left node being smaller than right node

## Growing a heap

* Can only ever add node at left-most available spot in tree i.e. left-most available node, at the lowest possible level
* Swap node "up" the tree, until both the order and shape properties are valid
  * So if node to insert is greater than parent in a max heap, swap with parent, etc. until it's no longer greater than parent

![growing-heap](https://cdn-images-1.medium.com/max/1200/1*BP0o8V34jxYE4Dn8byJqow.jpeg)

## Shrinking a heap

* Replace node that is removed with right-most node at lowest level
* "Bubble" node down the tree until the order is correct

![shrinking-heap](https://cdn-images-1.medium.com/max/1200/1*tq8hBeMDKPTvhfp9R_J45g.jpeg)

## How to implement a heap

* Representing a heap using an array
* Most important aspect of a heap is that max / min element is always the root node i.e. root node is always located at index `0` of an array
* Can always find child nodes of index `i` - left child is at `2i + 1` and right child is at `2i + 2`
* Node's parent is located at `Math.floor(i - 1 / 2)`

## What heaps are used for

* Used to represent a priority queue - a queue data structure where
  * every item has a priority associated with it (usually an integer)
  * item with high priority is dequeued before an item with low priority
  * two items with equal priority are dequeued based on their order in the queue
* Binary heaps are well-suited for being used as priority queues since it's very easy to know and retrieve/remove element with highest priority as it will always be the root node
* Adding or removing an element takes logarithmic time, since we eliminate half of the possible nodes with each level that is traversed
