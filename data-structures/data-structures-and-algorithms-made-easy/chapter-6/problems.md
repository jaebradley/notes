# Problems

## Find maximum element in binary tree

* Simple solution is to find maximum in left subtree, in right subtree, and compare with root

```javascript
function findMax(root) {
  let max = Number.MINIMUM_INTEGER;

  if (root != null) {
    const leftMax = findMax(root.left);
    const rightMax = findMax(root.right);
    const childMax = Math.max(leftMax, rightMax);
    const treeMax = Math.max(childMax, root.val);
    return Math.max(max, treeMax);
  }

  return max;
}
```

* Other way of doing this without using recursion is to traverse tree iteratively
* Simple way to do this is using level-order traversal and to update the max value as you inspect nodes

## Calculate size of binary tree

* Calculate size of left and right subtrees recursively and add 1 for current node, and return result to parent

```javascript
function size(root) {
  if (!root) {
    return 0;
  }

  return size(root.left) + size(root.right) + 1;
}
```

## Print level order data in reverse order

* Process right node first instead of left, as usual
* Push dequeued data to a stack

```javascript
function reverseLevelOrder(root) {
  if (root) {
    const queue = [root];
    const stack = [];

    while (queue.length > 0) {
      const currentNode = queue.dequeue();
      if (currentNode.right) {
        queue.enqueue(currentNode.right);
      }

      if (currentNode.left) {
        queue.enqueue(currentNode.left);
      }

      stack.push(currentNode);
    }
  }

  while (stack.length > 0) {
    const node = stack.pop();
    // process node
  }
}
```
