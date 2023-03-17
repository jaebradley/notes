# [Demystifying Depth-First Search](https://medium.com/basecs/demystifying-depth-first-search-a7c14cccf056)

* Searching through a tree usually means that we're doing it to check all the nodes in a tree or updating all nodes in a tree
* Don't want to search a node more than once
* Depth-first search means traversing a branch of the tree until a leaf is found and then working backwards from that point in time
* Strategies
  * Pre-order (`root`, `left`, `right`)
    * Read node, then visit entire left subtree before visiting entire right subtree
  * In-order (`left`, `root`, `right`)
    * Visit entire left subtree, then read node, then visit entire right subtree
  * Post-order (`left`, `right`, `root`)
    * Visit entire left subtree, then entire right subtree, then read node
  * Time complexity is `O(n)` since we'll be searching each node in the tree
  * Space complexity is `O(h)` where `h` is the height of the tree
    * This is because the DFS recursive function will be called again and again until a leaf is found (and the distance between the `root` and `leaf` nodes is the `height`)
