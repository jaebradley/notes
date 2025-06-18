# System Interactions

## Leases and Mutation Order
* Each mutation is performed across all the chunk's replicas
* Leases are used to maintain a consistent mutation order across replicas
* Master grants a chunk lease to one of the replicas, which we call the primary
* The primary picks a serial order for all chunk mutations
* All replicas follow this order when applying mutations
* Master may revoke leases before they expire like when the master wants to disable mutations on a file that is being renamed
* Even if the master loses communication with a primary, it can safely grant a new lease to another replica after the old lease expires

## Write Flow
* Client asks the master which chunkserver holds the current lease for the chunk and the locations of the other replicas
* In the case where no chunkserver as a lease, the master grants a lease to one of the replicas
* Master replies to the client with the identity of the primary and the locations of the other secondary replicase
* Client sends data to all replicas
* Each chunkserver stores data in an LRU buffer cache
* Decoupling the data flow from the control flow means that expensive data flow can be scheduled based on network topology independent of which chunkserver is the primary
* Once all replicase have acknowledged receiving the data, the client sends a write request to the primary
* Write request identifies previous data pushed to all replicas
* Primary assigns consecutive serial numbers to all the mutations it receives
* Primary receives mutliple mutations from multiple clients
* Primary applies mutation to its own local state in serial number order
* Primary forwards write request to all secondary replicas
* Each secondary replica appleis mutations in the same serial number order assigned by the primary
* Each secondary replies to the primary indicating that they have executed the mutation
* Primary responds to the client with any errors
* In the case where some subset of secondary replicas have failed to execute the mutation, the modified region is left in an inconsistent state
* Client code retries the failed mutations
