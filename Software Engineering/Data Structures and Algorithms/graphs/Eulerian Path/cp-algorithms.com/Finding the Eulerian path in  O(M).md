# [Finding the Eulerian path in `O(M)`](https://cp-algorithms.com/graph/euler_path.html)
* Eulerian path is a path in a graph that passes through all of its edges exactly once
* A Eulerian cycle exists if the degrees of all vertices are even
* A Eulerian path exists if the number of vertices with odd degrees is two (or zero, in the case of a Eulerian cycle)
* Graph must contain a connected graph if all isolated vertices are removed
* The recursive approach to finding the Eulerian path is
  * Iterate through all outgoing edges from vertex `V`
    * Remove edge from the graph
    * Call the `find Eulerian path` function from the second end of the edge
  * Add vertex `V` to the answer
* The non-recursive approach is
  * Start with a stack
  * Add some starting vertex to the stack
  * While stack is non-empty
    * For a vertex `v` at the top of the stack
    * If the degrees of `v` are zero
      * Add `v` to the answer (the circuit)
      * Pop `v` off the top of the stack
    * If the degrees of `v` are non-zero
      * Find outgoing edges from `v` 
      * Remove edges from the graph
      * Add second end (vertex) of the edge to the stack

## Domino problem
* `N` dominoes where a number is written on each end of the domino
* Put all dominoes in a row such that the numbers on any two adjacent dominoes are the same
* Dominoes are the edges in the graph and the numbers are the vertices
* Then the problem is whether there is a Eulerian path in the graph
