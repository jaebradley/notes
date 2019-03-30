# Shortest Path in Unweighted Graph

* Identify shortest path from input vertex `V` to all other vertices
* Effectively BFS with one additional data structure - distance table
  * Each row in table corresponds to vertex
  * Each column in table is distance from source vertex and a previous vertex
* Enqueue start vertex
* Set it's distance as `0` and previous node as `null`
* Dequeue start vertex - get it's adjacent vertices
* If vertices do not have a distance entry then it's first time seeing vertex
* Set the distance entry's distance value as the previously dequeued vertex's distance + 1
* Set the distance entry's previous node value as the previously dequeued vertex
* Enqueue the adjacent vertex
* In the future, if this vertex is seen again, no need to process, because it's already been seen so later vertex appearance is not going to be part of shortest path

```javascript
function shortestPath(vertex) {
  var distances = {};
  var queue = [];
  queue.enqueue(vertex);
  distances[vertex] = { distance: 0, previous: null };

  while (queue.length > 0) {
    var currentVertex = queue.dequeue();
    var distance = distances[currentVertex];

    for (var i = 0; i < currentVertex.vertices; i += 1) {
      var nextVertex = currentVertex.vertices[i];
      var nextVertexDistance = distances[nextVertex];
      if (!nextVertexDistance) {
        distance[nextVertex] = { distance: nextVertexDistance.distance + 1, previous: currentVertex };
        queue.enqueue(nextVertex);
      }
    }
  }
}
```
