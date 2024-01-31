# [Topological Sort](https://www.interviewcake.com/concept/java/topological-sort)

* Takes a directed graph and returns an array of nodes, where each node appears before all the nodes it points to (they are _topologically ordered_)
* Imagine a graph where node `1` points to node `2` and node `3`
  * Node `2` and node `3` point to node `4`
  * Node `2` points to node `5`
* The topological ordering for this graph would be `[1, 2, 3, 4, 5]`
  * Since node `2` and node `3`, node `4` appear before node `5`
* Cyclic graphs don't have valid topological orderings
  * It doesn't make sense for a node to be _before_ **and** _after_ some other node

## Algorithm

* The first node in the topological ordering can't have any incoming directed edges (i.e. an indegree of zero)
* When a node is added to the topological ordering, can take "remove" the node (and its outgoing edges) from the graph
* "Removing" the node's outgoing edges is meaningful as it helps determine the next set of nodes that will have an indegree of zero

## Implementation

* Identify node with no incoming edges
* Add node to the topological ordering
* "Remove" it from the graph
* Repeat until there aren't any more nodes with indegree zero
  * Assuming there are no cycles, when there aren't any more nodes with an indegree of zero, all nodes have been added to the topological ordering
* Obviously, don't modify the graph in-place by actually removing nodes
  * Use hash map to track each node's indegree
  * When a node is added to the topological ordering (aka "removed"), decrement the indegree of the node's neighbors
### Time and Space Complexity

* Determining the indegree for each node take `O(number of edges)` since each directed edge needs to be inspected once
* Finding nodes with no incoming edges
  * Initially loops through the indegree mapping
  * `O(number of nodes)`
* Add nodes until no more nodes with an indegree of zero
  * Executes once per node
  * Decrementing the indegree for each neighbor of the node added to the topological ordering
    * One decrement for each edge (should never consider an edge more than once)
    * So overall, this calcuation should take `O(number of edges)`
* Time complexity of `O(number of edges + number of nodes)` since all nodes and all edges have to be considered at least once
* Space complexity will be `O(number of nodes)`
  * Indegrees mapping will have one entry for each node in the graph
  * Queue of nodes with indegree zero (that continues to get populated) should have, in the worst case (graph with no edges), one entry per node
  * Topological ordering (array) will eventually have every node in it
