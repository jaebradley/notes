# [Minimum spanning tree - Prim's algorithm](https://cp-algorithms.com/graph/mst_prim.html)

* Weighted, undirected graph with `n` vertices and `m` edges
* To find the spanning tree of this graph which connects all vertices and has the least weight use Prim's algorithm
  * Spanning tree is a set of edges such that any vertex can reach any other by exactly one simple path

## Algorithm

* Add edge to minimum spanning tree one at a time
* Initially, choose one vertex to be in the MST
* For vertices not in the MST, calculate the minimum weight edge starting at the chosen vertex and ending at a vertex _not_ in the MST
* Continue this process where for each vertex in the MST, calculate the minimum weight edge that ends with a vertex not in the MST
* This process is continued until the MST contains all the vertices

## Implementations

### Dense Graphs O(n^2)

* Keep track of nodes in MST
* Build adjacency matrix of `n` x `n` edges, that stores the weight of each edge between two vertices
* Keep array that stores minimum weight from a vertex not in the MST to a vertex in the MST
* In each of the `n` iterations, the vertex with the smallest edge weight is selected and the minimum weight for all other vertices gets updated
