# Binary Search Trees

## Inserting

* Follow tree values until find appropriate place to insert value
* This will insert as a leaf node

```javascript
function insert(root, value) {
  if (!root) {
    root = new Node(value);
  } else {
    if (value < root.value) {
      root.left = insert(root.left, value);
    } else {
      root.right = insert(root.right, value);
    }
  }
  return root;
}
```

## Deleting

* May not only be deleting leaf nodes
* If deleting a leaf node, parent should have null reference
* If node has one child, then replace parent's reference to node with child
* If node has two children
  * Replace node with largest element in it's left subtree
  * Recursively delete that largest node (which cannot have a right child)

```javascript
function delete(root, value) {
  if (root) {
    if (value < root.value) {
      root.left = delete(root.left, value);
    } else if (value > root.value) {
      root.right = delete(root.right, value);
    } else {
      // If node has both children
      if (root.right && root.left) {
        // Replace node to delete with largest in left subtree (recursively)
        const largest = findMax(root.left);
        root.data = largest.data;
        root.left = delete(root.left, largest.data);
      } else {
        if (root.left) {
          root = root.left;
        } else if (root.right) {
          root = root.right;
        }
      }
    }
  }
}
```
