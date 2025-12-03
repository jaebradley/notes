# [Euler’s Algorithm](https://networkx.org/nx-guides/content/algorithms/euler/euler.html)
* For a directed graph to have an Eulerian path, it must have
  * At most one vertex where the difference between the vertex's out-degree and in-degree is 1
  * At most one vertex where the difference between the vertex's in-degree and out-degree is 1
  * Every other vertex has in-degree and out-degree values that are the same
  * All of the vertices belong to the same connected component
