# System Interactions

## Leases and Mutation Order

* Each mutation is performed at all the chunk's replicas
* Leases are used to maintain a consistent mutation order across replicas
* Master grants a chunk lease to one of the replicas, which we call the primary
