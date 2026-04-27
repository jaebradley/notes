# [How to build a distributed queue in a single JSON file on object storage](https://turbopuffer.com/blog/object-storage-queue)
* `queue.json` file stores all queued data
  * < 1 GB, so the file can fit in-memory
  * Uses object storage (like S3)
* Queue pusher reads the contents of the file and appends a new job at the end using a check-and-set
* Queue worker uses check-and-set to mark the first unclaimed job as in-progress
* Writes only succeed if `queue.json` has not changed since it was read
  * If it has changed, the client reads the new contents and tries again
  * Strong consistency without explicit locking
* This approach works for 1 request per second, but most queueing systems receive more than 1 request per second
* Whenever a write to object storage is in-flight, buffer incoming requests in memory
  * As soon as the in-flight write finishes, this in-memory buffer is flushed as the next check-and-set write ("group commit")
* There may be many writers trying to write to a single `queue.json` object at any given time
  * Since writes are non-overlapping due to the check-and-set semantics, and write latency is \~200 ms per write, can only support \~5 writes per second
* Stateless broker, that manages interactions with object storage
  * Broker runs a single group commit loop on behalf of all clients
  * It does not acknowledge a write until the group commit has written to object storage
  * Clients are blocked until its written data has been durably committed
  * While this broker is the bottleneck, the writes it processes from many clients are small
  * So most of the broker's work is holding open connections and buffering requests in memory while waiting on network I/O from object storage
* If a request from client -> broker takes too long, start a new broker
  * Write the broker's address to `queue.json`
  * If there are two broker's at any time, the check-and-set should ensure correctness even with two brokers
* For a worker that dies after claiming a job, add a heartbeat where a worker will confirm that it is still working on a job
  * This heartbeat is in the form of a timestamped message to the broker
  * This message is written to the `queue.json` for that job
  * If the last heartbeat for a job in the queue is ever more than some timeout, then assume the original worker is dead and the next worker takes over where it left off
