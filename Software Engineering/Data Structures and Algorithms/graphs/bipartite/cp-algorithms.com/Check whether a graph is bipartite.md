# [Check whether a graph is bipartite](https://cp-algorithms.com/graph/bipartite-check.html)
* Bipartite graph is a graph whose vertices can be divded into two disjoint sets
  * Every edge connects two vertices from different sets
  * In other words, there isn't an edge that connects vertices from the same set
* Easier to prove a graph is bipartite if it is two-colorable
* Article's algorithm uses BFS
* Start searching for each vertex that has not been visited yet
* Assign the starting vertex to side X
* Each time an unvisited neighbor is visited, assign it to opposite side as it's neighbor
* When we visit a previously visited neighbor, we check to see if it assigned to the opposite side (or the same side) as the current vertex
  * If the previously visited neighbor has the same side as the current vertex then the graph is not bipartite
  
