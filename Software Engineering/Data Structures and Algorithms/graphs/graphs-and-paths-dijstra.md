# [Graphs & Paths: Dijkstra](https://towardsdatascience.com/graphs-paths-dijkstra-4d8b356ad6fa)

* Create a visited set and a distance set
  * Initialize visited set values to `false`
  * Initialize distance set values to `infinity`
  * Example initial visited map: `{ SD: false, LA: false, NY: false }`
  * Example initial distance map: `{ SD: 0, LA: INT_MAX, NY: INT_MAX }`
* Update distance for current node (so if starting at SD, set SD to zero, and current node is SD)
* For nodes that neighbor SD, calculate distance
* If any of these nodes have a smaller calculated distance than the distance in the distances map, update the distance
* Mark current node as visited in visited map
* Next iteration starts by choosing a new current node
  * Next current node is the node with the smallest value in the distance map that is also false in the visited set (i.e. unvisited)
* When all reachable nodes have been visited, algorithm is complete

