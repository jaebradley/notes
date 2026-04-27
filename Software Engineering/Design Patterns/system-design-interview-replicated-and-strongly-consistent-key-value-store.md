# [System Design Interview: Replicated and Strongly Consistent Key-Value Store](https://levelup.gitconnected.com/system-design-interview-replicated-and-strongly-consistent-key-value-store-b690d8e15c9a)

* Strong consistency requires that a read always return the latest value written
  * If a write precedes a read, the read should return a value no older than the write
  * If a write and a read are concurrent, the read can either return the value before the concurrent write or the value of the concurrent write
  * If a read returns the value of a concurrent write, that means the concurrent write has taken effect, regardless of whether the write has returned or not
  * All subsequent reads should not return values older than the write

## Read-Impose-Write-Consult-Majority

### Writing

* With multiple concurrent writers, we need a way to determine which write is newer
* Newer writes always override older values
* Keep a logical timestamp in each machine and increment it on write
* Use one timestamp per each key in the key/value map
* The writer needs to first consult peers to find out the greatest timestamp at the moment
  * Broadcasts a write using that timestamp + 1
* Writer must consult a majority of machines when writing (and the write is only considered complete when it has propagated to a majority of machines)
* The consultation from the writer to its peers always returns the highest timestamp of all completed writes
  * This is because the completed writes must have completed propagation to a majority of the machines
  * So the consultation of a majority is guaranteed to overlap at least one machine with the completed writes
* For concurrent writers, it's safe for them to broadcast writes - strong consistency doesn't dictate a specific order as long as it has a consistent appearance to clients
  * This consistent appearance is guaranteed by the timestamp

### Reading

* Obviously, each machine can't just return its local value upon read
  * Each read will need to consult a majority of machines to find out the latest value
  * But just returning that latest value from quorum is not enough because the read may include the update from a concurrent write while a later read may consult a different majority that have not yet gotten the update
    * Read may include update from machines 1,2 which has some update while a later read includes update from machines 3,4 which have not gotten the update that machines 1,2 have been updated to
* Broadcast the quorum value from the read consultation to a majority of the machines before returning it
  * This ensures that later reads will always get values no older than the quorum value
  * If the quorum value is stale, rewriting it is a no-op because older writes will be ignored in machines with newer values
  * If the quorum value is the latest, a majority of machines will be force-updated before the quorum value is returned again
  * A later read which consults a majority of machines is guaranteed to come across the quorum value
  * Other concurrent reads may still return a value older than the quorum value - that's ok since strong consistency only says that reads that are invoked _after_ the return of a previous read must yield an equal or newer value
* Every read incurs two broadcasts and every write incurs two broadcasts

### Leader-Based Replication

* The machines elect a leader amongst themselves to handle all client write requests
  * If a follower receives a client write request, it'll forward it to the leader
  * Values only float from the leader to the followers
  * Multiple write requests may arrive at the leader simulatenously
  * The leader will serialized them in the log and replicate them in the same order to the followers
* An entry is only considered committed when it is replicated in a majority of machines
* Get requests also go to the leader since it has the latest commit index offset and therefore maintains the most up-to-date key-value store
  * If the leader is segregated from its followers, the followers may decide to elect a new leader in which case the old leader's local values have become stale
* The leader needs to consult a majority of the followers to confirm leadership status before returning local values
* This "leased-leader-based replication" is more performant than the read-import write-consult majority strategy because once the leader is elected, writes only require one round of broadcast from the leader to the followers
  * Reads also don't "impose" values back to a quorum, saving one round of broadcasts as well
* Leader could serve the reads entirely from local key/value store if it remains leader of the cluster, which should be the case as long as the leader does not fail and can still reach a majority of followers

#### Leases

* The leader needs to send heartbeats to the followers
* If a follower does not hear back from the leader after some timeout period, the follower will assume the leader is dead and it will run for an election to try and become the next leader
* The leader maintains a countdown timer for each follower
  * Every time the leader sends a heartbeat to a follower it resets the follower countdown timer to `X` seconds
  * If follower does not respond with `OK` the leader sets the timer for that follower to `0`, meaning it has lost the support from that follower
* Leader reads from its local values as long as a majority of the countdown timers are not set to `0`
* Every follower also maintains a countdown timer and sets it to `X` every time it receives a heartbeat from the leader
  * The follower remains a follower until its countdown timer runs out
  * Since the follower countdown starts when it receives a heartbeat from the leader and since the leader countdown starts when it sends the heartbeat, the countdown on the leader is more conservative and can be relied upon to determine if the leader still has the leadership leases
  * A new leader will only be elected when a quorum of followers' countdown timers expire

#### Read-Heavy Workload

* For read-heavy workloads, may want the followers to share the read load
* When a read is invoked in a follower, the follower performs a quorum read against a majority of the machines
  * Rather than returning the values, the timestamps are returned to the follower
  * The follower checks if the highest timestamp from the quorum read is larger than its own timestamp
  * If other machines have a larger timestamp, that means a write is pending replication
  * Note that the write could still be ongoing or it could have completed
  * It's expensive for the follower to find out exactly which case it is
  * Error the follower out - ensures that reads err on the side of being conservative and never return inconsistent values
* How to validate that the key/value store is strongly consistent
  * Collect logs from all machines, sort based on invocation time, and build a dependency graph
  * Dependency graph captures information of which read reflects which write and chronological order
  * The dependency of the reads/writes can be determined because each write sets a unique value in the test
  * Cycles in the graph suggest violation of strong consistency
