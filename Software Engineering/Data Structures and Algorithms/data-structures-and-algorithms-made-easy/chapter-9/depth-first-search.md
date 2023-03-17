# Depth First Search

* Similar to pre-order traversal
* All vertices are marked as unvisited
* Starts at vertex `v`
* Consider edges to other vertices
* If an edge leads to already visited vertex then backtrack to vertex `v`
* If an edge leads to an unvisited vertex process that vertex and apply same process to that vertex\
* Time complexity is `O(Vertices + Edges)` is using adjacency list to represent graphs
  * Since "visited vertices" list should prevent double-counting

```javascript
function dfs(vertex, visitedVertices) {
  visitedVertices = visitedVertices.push(vertex);
  for (var i = 0; i < vertex.vertices.length; i += 1) {
    if (!visitedVertices.includes(vertex)) {
      dfs(vertex, visitedVertices);
    }
  }
}

function traversal(startVertex) {
  var visitedVertices = [];
  dfs(startVertex, visitedVertices);
}
```
