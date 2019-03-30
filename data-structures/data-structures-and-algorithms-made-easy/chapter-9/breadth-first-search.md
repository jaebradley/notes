# Breadth First Search

* BFS is similar to level-order traversal
* All vertices are marked as unvisited
* Starts at a vertex
* Visits all vertices that have edges to start vertex
* Then visits all vertices that have edges to the previous vertices, etc.
* This is done by dequeuing a vertex from the queue
  * Marking it as visited
  * Identifying the neighboring vertices
  * Enqueuing those vertices in the queue
  * Continuing this process until queue is empty
* Time complexity is also `O(Vertices + Edges)`
* Has to store all child pointers at each level (higher memory requirements than DFS)

```javascript
function traversal(vertex) {
  var visitedVertices = [];
  var queue = [];
  queue.enqueue(vertex);

  while (queue.length > 0) {
    var currentVertex = queue.dequeue();
    // process current vertex
    visitedVertices.push(currentVertex);
    for (var i = 0; i < currentVertex.vertices.length; i += 1) {
      var nextVertex = currentVertex.vertices[i];
      if (!visitedVertices.includes(nextVertex)) {
        queue.enqueue(nextVertex);
      }
    }
  }
}
```
