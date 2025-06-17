# System Interactions

## Leases and Mutation Order
* Each mutation is performed across all the chunk's replicas
* Leases are used to maintain a consistent mutation order across replicas
* Master grants a chunk lease to one of the replicas, which we call the primary
* The primary picks a serial order for all chunk mutations
* All replicas follow this order when applying mutations
* Master may revoke leases before they expire like when the master wants to disable mutations on a file that is being renamed
* Even if the master loses communication with a primary, it can safely grant a new lease to another replica after the old lease expires

## Control Flow
