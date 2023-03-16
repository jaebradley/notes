# [Breaking Down Breadth-First Search](https://medium.com/basecs/breaking-down-breadth-first-search-cebe696709d9)

* Depth-first search on a tree involves working down one branch of the tree until a leaf is hit, and then working way backtracking until all nodes are hit
* Example of how DFS would work on a binary tree where the values indicate the order in which certain nodes would be searched (pre-order traversal)

     1
    /  \
   2    5
  / \  /  \
 3   4 6   7

* call stack
  * [1] - start with root
  * [1, 2] - go left
  * [1, 2, 3] - go left
  * [1, 2, 4] - since popping off stack is leaf, we go "right" for node 2 and explore 4
  * [1, 5] - pop off 4, which also means that since 2 has been fully explored (i.e. searched left and right nodes it too gets popped off call stack); now we push the root's right node, 5
  * [1, 5, 6]
  * [1, 5, 7]
  * [1]

* BFS traverse through level of child nodes first before moving on to any children
* Different than DFS where went to children after evaluating current node
  * Instead, the process is
  * Visit node
  * Add left child to queue
  * Add right child to queue
  * Remove node from queue
* So in the above tree (keeping pre-order traversal) it'd be
  * [1] - enqueue root
  * [5, 2] - enqueue left, then right, and dequeue 1
  * [4, 3, 5] - enqueue 2's left and right, then dequeue 2
  * [7, 6, 4, 3] - enqueue 5's left and right, then dequeue 5
  * Remove each leaf from queue until empty
* Since we're visiting every node once, the time complexity is O(n)
* Space complexity depends on size of queue at it's worst, which could be O(n) (all nodes are direct children of root, for example)

```javascript
const queue = [root];

while (queue.length > 0) {
  const node = queue[0];

  if (node.left) {
    queue.push(node.left);
  }

  if (node.right) {
    queue.push(node.right);
  }

  queue.shift();
}
```
