# [Disjoint Set Union](https://cp-algorithms.com/data_structures/disjoint_set_union.html)
* Disjoint Set Union or Union Find typically merges sets and can identify what set contains a specific element in (almost) constant time, on average

## Build an efficient data structure
* Sets are stored as trees where each individual tree corresponds to one set
* Root of the tree is the "leader" for the set
* Initially, every element is a single set before these sets are merged / unioned

## Naive implementation
* A `parents` array that contains each elements ancestor
  * Initially, when every element is in its own set, every element's `parent` value is itself
* To find the root ancestor of a set ("set representative" or "set leader"), recursively traverse the `parents` array
  * Find the ancestor of the set, then find the ancestor of the ancestor, etc until the current element is equal to the parent of that element
* To union two sets, find the root ancestor of the first set and the second set
  * If they are not the same, then arbitrarily pick one's root ancestor to be the root ancestor of the other
  * Inefficient as trees degenerate to long chains and thus finding the root ancestor of a set has a runtime of `O(n)`

## Path compression optimization
* Instead of recursively traversing each ancestor when finding the set representative, set the parent of each ancestor along the path to the set representative directly
  * Runtime is now `O(log n)` on average
```
int find_set(int v) {
    if (v == parent[v])
        return v;
    return parent[v] = find_set(parent[v]);
}
```

## Union by size / rank
* There are two popular approaches to avoiding the previously mentioned "long chains" created when naively merging sets
  * Use the size of the tree or use the depth of the tree
  * Whichever tree has the lower rank is attached to the tree with the bigger rank
* Keep track of the size / rank of each set in a separate array / hash map

## Time complexity
* Final amortized time complexity with path compression and union by size / rank leads to runtime of `O(inverse Ackernmann function(n))` 
* The inverse Ackermann function grows very slowly - for example, it doesn't exceed `4` for `n < 10^600` 
* For a single method execution, it may have a worst-case runtime of `O(log n)`  but across `m` such executions, it will end up with the average runtime

## Connected components in a graph
* Problem is usually defined by starting with an empty graph
  * Add vertices and undirected edges
  * Answer whether or not some vertices `A` and `B` are in the same connected component within that graph

## Search for connected components in an image
* An image of `m` x `n` pixels
* Originally, all pixels are white but a few black pixels are drawn
* Find the size of each white connected component in the final image
* Solution is to iterate over all white pixels in the image and for each neighbor, union / merge the neighbor's set with the current set
* The resulting trees in the disjoint set union are all the distinct sets of white pixel connected components
* While the problem can be solved by D/BFS, disjoint set union has a memory advantage by only needing to process the previous row and the current row at any given time

## Checking bipartiteness
* Given graph, is the connected component containing vertex `X` bipartite?
* Store parity of path up to the set representative for each vertex
* Then can check if adding an edge leads to a violation of bipartiteness
  * If the ends of the edge are in the same connected component and have the same parity length as the set representative then adding an edge will produce a cycle of odd length and the component will lose its bipartiteness
* When adding an edge, if both ends of the edge have the same set representative, and if traversing from one end has the same odd/even-ness as traversing from the other end, then it cannot be bipartite
* Merging sets when adding an edge means merging the parity of both sets
  * `Parity of first set XOR Parity of second set XOR 1`
