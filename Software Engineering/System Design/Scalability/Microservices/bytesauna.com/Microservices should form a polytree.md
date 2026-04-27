# [Microservices should form a polytree](https://bytesauna.com/post/microservices)
* Polytree is a type of graph
  * Directed acyclic graph
  * When drawn as an undirected graph, it is a tree (i.e. it is connected and acyclic)

<img width="751" height="662" alt="image" src="https://github.com/user-attachments/assets/6b7c5b49-75d9-455a-92c9-e9aa8765c43b" />

* Cyclical microservice dependencies
  * Resource usage can spiral as services retry / hold resources while waiting for responses that depend on its own work
  * Cycles are a symptom of tangled responsibilities
  * When a set of microservices form a cycle, it begs the question whether the services should be a single service or whether functionality should be rearranged to form a tree instead
* No directed cycle of microservices, but an undirected cycle of microservices
  * May be a sign of tangled responsibilities
  * Should `n2` and `n3` be a single service or should `n4` be two different services?

<img width="461" height="472" alt="image" src="https://github.com/user-attachments/assets/5c67bae6-8b31-4d8d-b6b9-823862ab05bc" />

## Benefits of a polytree
* Data and behavior have a single unambiguous home
  * In other words, if behavior changes there should be an easy way to identify exactly where to look
* A node failure can only impact nodes below it, and it must terminate after a finite number of steps
* Engineers can understand systems by starting at the leaves and working upwards
* With no hidden cycles, teams can change versions and/or redeploy services with less risk of unexpected breaking behavior
