# [Ace the System Design Interview — Distributed ID Generator](https://towardsdatascience.com/ace-the-system-design-interview-distributed-id-generator-c65c6b568027)

* Sequential IDs have inherent information, like ordering, whereas random UUIDs have no obvious semantic meaning
* Requirements of the system are: 10 million sequential IDs per second, IDs must preserve some level of ordering information, can toggle generation rate up/down

## High-Level Design

### API Design

* Can imagine the interface is something like `get_uid_batch(batch_size: int)` and returns a `List[int64]`
* Client can ask for some IDs every X milliseconds storing them in local memory and then hand them out from local memory to avoid network connection overhead
  * The IDs stored in local memory will be lost if the client crashes, but this is acceptable since there are plenty of IDs "to waste"

### High-level Architecture

* Can use many computers working concurrently to meet demand of high generation volume
* Cluster manager to monitor node health and workload - if demand exceeds supply, the manager will add more workers (can use auto-scaling software like Kubernetes)

### Control Logic

* Start ZK service & cluster manager (Kubernetes)
* Cluster manager creates X workers
* Each worker registers with ZK and gets a worker ID (integer from 0 to 256)
* Each worker corresponds to an ephemeral node in ZK
* Workers send CPU to cluster manager who removes / adds workers based on consumption rate

## Details

### UID Decomposition

* If generating 10 million UIDs per second, need UIDs of at least 56 bits, which will be exhausted in 228 years
* Use timestamp as part of UID so that it can be sortable
  * UNIX timestamp has 64 bits, which is too long for our UID length of 56 bits
  * Establish a custom Epoch that starts at Time X instead of midnight 1970/1/1, and count milliseconds since then
  * A timestamp with 40 bits will overflow in ~35 years, which is long enough for the project
* Two machines will often generate UIDs with the same timestamp, so need to add more information
  * The worker ID (0 to 256)
  * The thread ID
  * Local counter since a single thread can generate two timestamps in one millisecond, so use a thread-local counter to further distinguish between two IDs
    * When counter is full, thread sleeps through the current millisecond

### Why not queues

* Some queues don't disclose the number of messages buffered, making it impossible for our service to ramp up/decrease capacity when the demand goes up/down

