# [In Search of an Understandable Consensus Algorithm (Extended Version)](https://raft.github.io/raft.pdf)

## Replicated State Machines

* State machines on a collection of servers compute identical copies of the same state
* Typically implemented using a replicated log
  * Each server stores a log containing series of commands
  * Each log contains the same commands in the same order
  * Each state machine processes the same sequence of commands

## Raft Consensus Algorithm

* Raft elects a leader
* Leader is responsible for managing the replicated log
  * Leader accepts log entries from clients
  * Replicates commands to other servers
* When a leader fails or becomes disconnected to followers, a new leader is elected

### Server Roles

* Cluster contains many servers, allowing the system to tolerate `N/2 - 1` server failures
* A server is either a leader, follower, or candidate
* Followers are passive
  * They do not issue any requests
  * Only respond to requests from leader and candidates
  * If a client makes a request to a follower, the follower redirects the request to the leader

### Terms

* Raft divides time into terms of arbitrary length
  * Terms are auto-incrementing integers
* Every term starts with an election
* Once a candidate wins election, it serves as the leader for the rest of the term
* When a split vote occurs (i.e. no server wins a majority vote) the term ends with no leader
  * A new term, with a new election begins
* Every server stores a current term number
  * This current term value is exchanged whenever servers communicate
  * If one server's current term is smaller than another's, it updates its current term to the larger value
  * If a candidate or leader discovers that its term is out of date, it immediately becomes a follower
  * If a server receives a request with a stale term, it rejects the request

### Leader Election

* Server remains a follower as long as it continues to receive valid communication from a leader or candidate
  * Leaders send periodic heartbeats to all followers in order to continue to be elected
  * A follower that does not receive communication over some period of time assumes that a valid leader does not exist, and begins an election
* Candidate wins election if it receives votes from a majority of the servers in the full cluster
* Each server votes on a first-come-first-served basis
* While waiting for votes, a candidate can receive communication from another server claiming to be the leader
  * If the leader's term is at least as large as the candidate's current term, then the candidate recognizes the leader as legitimate and the candidate transitions to a follower
  * If the claimed leader's term is smaller than the candidate's term, the candidate server rejects the request and continues to be a candidate
* Raft uses randomized, relatively short election timeouts to ensure that split votes are rare and resolved quickly
  * Election timeouts are usually chosen randomly from 150-300ms
  * In most cases, a single server will time out and then immediately wins election, and send heartbeats before another server times out

### Log Replication

* Each client request contains a command to execute a state machine command
* Leader appends command to its log
  * Issues Append Entries command in parallel to followers to replicate the client command
* Once entry is safely replicated, leader applies entry to its own state machine
* Follower crash or network packet loss will lead to leader retries of the Append Entries command

```bash
# Example Logs with Follower Drift from Leader Log

| Term 1    |  Term 1    | Term 2    | Term 3    | Term 3    | Term 3    | Leader Log
| Command A |  Command B | Command C | Command D | Command E | Command F |

| Term 1    |  Term 1    | Term 2    | Follower A Log
| Command A |  Command B | Command C |

| Term 1    |  Term 1    | Term 2    | Term 3    | Term 3    | Term 3    | Follower B Log
| Command A |  Command B | Command C | Command D | Command E | Command F |

| Term 1    |  Term 1    | Follower C Log
| Command A |  Command B |
```

* Log entries are committed once the leader replicates the entry across a majority of servers
  * This process also commits all preceding entries in the leader's log, including entries created by previous leaders
* Leader keeps track of the greatest index that it knows has been committed
  * This max commit index is used in Append Entires commands to followers
* Log Matching Property
  * If two entries in different logs have the same index and term, then they will store the same command
  * If two entries in different logs have the same index and term, then the logs are identical in all preceding entries
    * Property guaranteed by the fact that when a leader sends a follower an Append Entries command, the leader includes the index and term of the entry in its log that immediately preceds the new entries
    * If the follower does not have an entry in its log with the same index and term, then it refuses the new entries
    * So when the Append Entries command succeeds, the leader knows that the follower's log entries are identical to its own log

#### Leader Inconsistency

* Leaders handle log entry inconsistency by forcing the followers' logs to duplicate their own
* Leader identifies the latest log entry that is shared between leader and follower
  * Leader deletes entries in the follower's log after the shared entry
  * Leader sends the additional entries from its log to the follower, and the follower applies these entries
* Leader maintains an index value for each follower
  * This index value is the index of the next log entry the leader will send to that follower
  * When a leader is first elected, it sets the index values to the index value after the leaders' last index value
* If a follower has an inconsistent "next" log entry value vs. the leaders' "next" log entry for that follower, the Append Entries command will fail
  * When this command fails, the leader will decrement its "next" log entry value and retries the Append Entries command
  * If the command fails, the leader continues decrementing its "next" log entry until the follower and leader "next" index entry values will match
  * At this point, the command will succeed, removing any conflicting entries from the follower's log and adding the leader's log entries
