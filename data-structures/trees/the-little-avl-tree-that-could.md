# [The Little AVL Tree That Could](https://medium.com/basecs/the-little-avl-tree-that-could-86a3cae410c7)

* Balanced trees are good because they are efficient (i.e. logarithmic) data structures
* An unbalanced tree means that we don't cut out half of the tree as we search through it (like in a binary search tree)

```bash
    3
   /
  4
 /
5
```

* An `AVL` tree is a self-balancing binary search tree
  * Rearranges itself to be height-balanced whenever the structure is modified
* `height` of a tree is the number of nodes on the longest path from the `root` to the `leaf`
* A binary tree is balanced if any two sibling subtrees do not differ in height by more than one level
  * Any two leaves should not have a difference in depth more than one level
* The difference between the heights of two subtrees in an `AVL` should never exceed `1` level (known as `balance factor`)

```bash
# Balanced Tree
# Difference between left and right subtrees are not more than one level

    1
   /  \
  2    3
 / \  /  \
4   5 6   7
       \
        8

# Unbalanced Tree
# Height difference between node with 2 and node with 3 is more than one level
    1
   /  \
  2    3
      /  \
     6   7
      \
       8
```

## Rotations

### Single

```bash
# Left rotation
# This is done when a node is inserted into the right subtree of a right subtree
# And causes the tree to become unbalanced

# Convert this
1
 \
  2
   \
    3

# to this
   2
 /   \
1     3
```

```bash
# Right rotation
# This is done when a node is inserted into the left subtree of a left subtree
# And causes the tree to become unbalanced

# Convert this
    3
   /
  2
 /
1

# to this
   2
 /   \
1     3
```

### Double

```bash
# Left-Right rotation

# Convert this

  3
 /
1
 \
  2

# To this, using left rotation on 1->2

    3
   /
  2
 /
1

# Then (we've seen this before) convert to this using right rotation

   2
 /   \
1     3
```

```bash
# Right-Left rotation

# Convert this

3
 \
  1
 /
2

# To this, using right rotation on 1->2

3
 \
  2
   \
    1

# Then (we've seen this before) convert to this, using left rotation

   2
 /   \
1     3
```
