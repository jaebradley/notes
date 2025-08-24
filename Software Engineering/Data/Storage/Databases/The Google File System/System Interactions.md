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

## Data Flow
* Control flows from client -> primary -> secondaries while data is pushed linearly along a carefully selected chain of chunkservers in a pipelined fashion
* To fully utilize each machine's network bandwidth, data is pushed linearly along a chain of chunkservers
  * This is in contrast to using another structure, like a tree
* Each machine's full outbound bandwidth is used to transfer data as quickly as possible rather than divided amongst multiple recipients
* To avoid network bottlenecks and high-latency, each machine forwards data to the "closest" machine in the network topology that has not yet received it
* Network topology is simple enough that distances can be estimated through IP addresses
* Data is transferred over TCP connections
* Once a chunkserver receives data, it starts forwarding data immediately
* Switched network with full-duplex links
  * Sending data immediately does not reduce the data receiving rate
* Ideal elapsed time for transferring `B` bytes to `R` replicas is `Bytes / Network Throughput` + `Replicas x Latency to transfer bytes between machines`
  * Inter-machine network latency is < 1 ms
  * Network throughput is 100 MBPS
  * 1 MB can be distributed in ~80ms

## Atomic Record Appends
* Traditionally, write operations are specified by the client using some offset where the data is written
* Concurrent writes to the same region result in the record containing data fragments from multiple clients
* Record appends consist of the client specifying the data to write and no offset
* GFS appends the data to the file at least once atomically, as a sequence of bytes
  * This append occurs at an offset of GFS's choosing and the offset is written to the client
  * Similar to writing to a file opened in `O_APPEND` mode in Unix, without the race conditions when there are multiple concurrent writers 
* For record appends, the client pushes the data to all replicas of the last chunk of the file
  * Sends request to the primary
  * Primary checks to see if appending the record to the current chunk would cause the cunk to exceed the maximum size (64 MB)
  * Pads the chunk to the maximum size, tells secondaries to do the same, and replies to the client indicating that the operation should be retried on the next chunk
  * Most likely, the record will fit within the maximum chunk size, meaning that the primary appends the data, tells the secondaries to write data at the exact offset, and replies with a success to the client
* If a record append fails, the client retries the operation
* Replicas of the same chunk may contain different data and possibly include whole or partial duplicates of the same record
  * GFS does not guarantee that all replicas are bytewise identical
  * Only guarantees that the data is written at least once as an atomic unit

## Snapshot
* When the primary receives a snapshot request, it revokes any outstanding leases on the chunks in the files it is about to snapshot
  * Any subsequent writes to these chunks requires an interaction with the primary to find the lease holder
* Example: the first time a client wants to write to a chunk C after the snapshot operation, the client sends a request to the primary to find the current lease holder
  * Primary notices that the reference count for chunk C is greater than one
  * Defers replying to the client request and picks a new chunk handle C'
  * Primary asks each chunkserver that has a current replica of C to create a new chunk called C'
  * By creating the new chunk on the same chunkservers as the original, we ensure that the data can be copied locally and not over the network
    * Disks are about 3x faster than 100 MB Ethernet links
  * Primary handles request by granting a replica a lease on the new chunk C' replying to the client
  * Client can write to the chunk normally, and the fact that a new chunk has been created from an existing chunk is abstracted away from the client
