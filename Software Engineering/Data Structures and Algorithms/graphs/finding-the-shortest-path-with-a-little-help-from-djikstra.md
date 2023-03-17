# [Finding The Shortest Path With A Little Help From Djikstra](https://medium.com/basecs/finding-the-shortest-path-with-a-little-help-from-dijkstra-613149fbdc8e)

* A weighted graph is a graph whose edges have some sort of value that is associated with them
  * Common to refer to this weight as the cost or distance between two nodes
* So in adjacency matrix that represents graph, tweak definition of the linked list that holds the edges
  * Each element in linked list contains two values - one is destination node index and second is weight of edge

## Djikstra's Algorithm

* Used to determine shortest path from one node in a graph to every other node within the same graph data structure, provided that the nodes are reachable from the starting node
* Process
  * When setting out to visit new node, choose the node with the smallest known distance / cost to visit first
  * Once at node to visit, check neighboring nodes
  * For each neighboring node, calculate distance / cost of node by summing the cost of edges leading to node **and the cost of the edges leading to current node**
  * If any of the calculated costs are smaller than the current minimum cost of the neighboring node, update the minimum cost of the node and update the previous node value for the neighboring node
* Initialization
  * Pick node to start
  * Create table of vertices, with shortest distance from node to start, and previous vertex
    * For all vertices except node to start, the shortest distance will be unknown / infinity and the previous vertex will be null / unknown
    * For starting node, the shortest distance is 0 and the previous vertex is itself
  * Keep track of unvisited and visited nodes using two sets (or one if keeping track of just one of the states)
* Since starting node has shortest distance of any unvisited nodes (since just started) look at neighboring nodes from starting node and calculate distance for them
  * Again, this is the cost of current node (starting node in this case) + cost from going from current node to the neighboring node
  * If this cost is smaller than the existing cost on the neighboring node update the smallest cost of the neighboring node and update the previous node of the neighboring node
  * After going through this process for all neighboring nodes mark the starting node as visited
* At the end of this process, got every single shortest path that stems from starting node
* If want to find the shortest path from some node to starting node can traverse the path using previous vertex values
