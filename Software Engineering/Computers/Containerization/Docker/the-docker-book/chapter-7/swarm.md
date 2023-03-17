# Swarm

* Pool of Docker hosts into a single virtual Docker host
* This cluster of hosts is integrated with a standard Docker API
* Has manager and worker nodes
  * Managers dispatch and organize work on swarm
  * Each unit of work is called a task
  * Multiple managers can exist though one manager will always be leader
  * Worker nodes run tasks dispatched by manager node
  * Tasks are defined by services
  * Services consist of container image and a set of commands to execute
* In example there are three nodes in cluster (larry, curly, moe)
* larry is manager while curly and moe are workers
* Each node in cluster runs a Swarm node agent
* This agent registers it's Docker daemon with the cluster
* Get nodes to join swarm by providing a manager token or a worker token

## Services

* Example swarm service creation 
  * `docker service create replicas 2 name heyworld ubuntu /bin/sh c "while true; do echo hey world; sleep 1; done"`
  * Names the service `heyworld`
  * `heyworld` runs the ubuntu image
  * It executes a `while` loop and echose a string
  * `replicas` flag controls how many tasks are run on the swarm
* Can see via `docker service ps` that it should be running on `larry` and `moe`
* Can scale up the service like `docker service scale heyworld=3`
  * Should now see `curly` also running the service
* Can specify a service as global, which will run on every worker in swarm (`--mode global`)
