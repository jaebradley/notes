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

## Same Trees

* If both nodes are `null`, then true
* If one is `null` but other is not, then false
* If both are not `null`, then compare data and recursively check left and right subtrees

```javascript
function check(firstNode, secondNode) {
  if (firstNode === null && secondNode === null) {
    return true;
  }

  if (firstNode === null || secondNode === null) {
    return false;
  }

  return firstNode.val === secondNode.val
    && check(firstNode.left) === check(secondNode.left)
    && check(firstNode.right) === check(secondNode.right);
}
```

## Mirror image tree

```javascript
function mirror(root) {
  if (root) {
    mirror(root.left);
    mirror(root.right);

    const left = root.left;
    root.left = root.right;
    root.right = left;
  }
}
```

## Two traversal sequences construct binary tree uniquely

* Must have inorder traversal as one of the traversal sequences
* Because other traversals do not distinguish between

```bash
  A
 /
B

A
  \
   B
```

## Print ancestors for a node

* If left or right of node is node to identify, then print current node's data
* Else if not node, then recursively call print function for left node, and then recursively call print function for right node
  * If right or left is ancestor, then print current node (since it's ancestor of ancestor)

```javascript
function print(root, node) {
  if (root) {
    if (
      root.left === node
        || root.right === node
        || print(root.left, node)
        || print(root.right, node)
    ) {
      // print node data here
      return true;
    }
  }

  return false;
}
```

## How many different binary trees are possible with `n` nodes

* `2^n - n`
