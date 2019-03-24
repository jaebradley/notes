# Traversals

       1
    /     \
  2        3
 /  \    /   \
4    5   6    7

## Preorder (NLR)

* Each node is processed before either of the subtrees
* 1, 2, 4, 5, 3, 6, 7
* Recursive version uses call stack to keep track of right tree execution after left tree finishes

```javascript
function preorderSearch(root) {
  if (root) {
    // process node
    preorderSearch(node.left);
    preorderSearch(node.right);
  }
}
```

* Iterative version needs to use stack in same way as call stack
  * After processing current node, push current node on stack
  * Process left subtree
  * After completing subtree processing, pop stack to get node
  * Process right subtree
  * Continue until stack is not empty

```javascript
function preorderSearch(root) {
  const stack = [];
  let currentNode = root;

  while (true) {
    while (currentNode) {
      // process current node
      stack.push(currentNode);
      currentNode = currentNode.left;
    }

    if (stack.length <= 0) {
      break;
    }

    // Left subtree is done processing so now process right subtree
    currentNode = stack.pop();
    currentNode = currentNode.right;
  }
}
```

## Inorder Traversal (LNR)

* 4, 2, 5, 1, 6, 3, 7

```javascript
function inorderSearch(root) {
  if (root) {
    inorderSearch(node.left);
    // process node
    inorderSearch(node.right);
  }
}
```

* Inorder is similar to preorder search but process current node after popping

```javascript
function inorderSearch(root) {
  const stack = [];
  let currentNode = root;

  while (true) {
    while (currentNode) {
      stack.push(currentNode);
      currentNode = currentNode.left;
    }

    if (stack.length <= 0) {
      break;
    }


    // Left subtree is done processing so now process node
    // then process right subtree
    currentNode = stack.pop();

    // process current node
    currentNode = currentNode.right;
  }
}
```

## Postorder (LRN)

* 4, 5, 2, 6, 7, 3, 1

```javascript
function postorderSearch(root) {
  if (root) {
    postorderSearch(node.left);
    postorderSearch(node.right);
    // process node
  }
}
```

* Unlike previous traversals, in the iterative version will be visiting node twice - once to process right subtree, and once to process node itself
* So need to distinguish between the two ways
* If use variable to keep track of previous node, then if previous node is current node's parent, then we are traversing down
  * In this case, process current's left node if available, else process right node
  * If neither are available (leaf) then process node itself
* If previous node is current node's left child, traversing back up the tree after processing left subtree
  * So process right child (push right child onto stack)
* If previous node is current node's right child, traversing back up tree from right, so pop off current and process it

```javascript
function postorderSearch(root) {
  const stack = [root];
  let currentNode = root.left;
  let previousNode;

  while (stack.length > 0) {
    while (currentNode != null) {
      stack.push(currentNode);
      currentNode = currentNode.left;
    }

    while (currentNode == null && stack.length > 0) {
      currentNode = stack.peek();
      if (currentNode.right == null || currentNode.right == previous) {
        // process node
        stack.pop();
        previous = currentNode;
        currentNode = null;
      } else {
        currentNode = currentNode.right;
      }
    }
  }
}
```

## Level-Order Traversal

* 1, 2, 3, 4, 5, 6, 7

```javascript
function levelOrder(root) {
  const queue = [];
  if (!root) {
    return;
  }

  queue.enqueue(root);

  while (queue.length > 0) {
    const node = queue.dequeue();

    // process current node

    if (node.left) {
      queue.enqueue(node.left);
    }

    if (node.right) {
      queue.enqueue(node.right);
    }
  }
}
```
