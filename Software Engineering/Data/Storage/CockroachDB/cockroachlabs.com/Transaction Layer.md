# [Transaction Layer](https://www.cockroachlabs.com/docs/v25.4/architecture/transaction-layer)

## Writes
* Write intents are replicated via Raft
  * They are a combination of a provisional value and an exclusive lock
* Unreplicated locks, associated with a transaction's writes, represent a provisional, uncommitted state
* Locks are stored an in-memory, per-node lock table and managed by concurrency control manager
* Transaction record is stored in the range where the first write occurs, including the transaction's current state (pending, staging, committed, aborted)
* As write intents are created, there are checks for a newer committed value
* If a newer committed value exists, the transaction may be restarted
  * If existing write intents or locks exist on the same keys, the transaction is resolved as a transaction conflict
  
## Reads
* If a locking read encounters any existing locks, the operation is resolved as a transaction conflict
* Strongly-consistent reads by default
  * Reads go through the leaseholder and see all writes performed by writes that committed before the reading transaction (serializable isolation) or statement (read committed isolation)
* Stale reads are faster and do not need to go through the leaseholder
  * They read from a local replica at a timestamp that is never higher than the closed timestamp
* Read committed isolation, by default, create exclusive locks on a row
  * Only one transaction can hold an exclusive lock on a row at a time
  * Only the transaction holding the exclusive lock can write to the row
  * Exclusive locks are replicated via Raft
