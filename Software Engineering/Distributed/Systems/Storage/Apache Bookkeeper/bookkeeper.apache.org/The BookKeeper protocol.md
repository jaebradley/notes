# [The BookKeeper protocol](https://bookkeeper.apache.org/docs/development/protocol/)
* A replicated log consists of an ordered list of ledgers
* Ledgers are composed of metadata and entries
* Metadata stored in ZooKeeper, which provides compare-and-swap operation
* Ledger entries are stored on storage nodes known as bookies
* Ledger has a single writer and multiple readers

## Ledger Metadata
* Ensemble size: # of nodes the ledger is stored on
* Write quorum size: # of nodes each entry is written to - the max replication for an entry
* Ack quorum size: # of nodes an entry must be acknowledged on - the min replication for the entry
* Ledger status: Open, closed, or in recovery
* Last entry: `NULL` if the current state is *not* closed
* One or more fragments
  * Each fragment is either the first entry of a fragment or a list of bookies for the fragment
* Ensemble size must be larger than or equal to the write quorum size which must be larger than or equal to the ack quorum size

## Ensembles
* When a ledger is created, ensemble size bookies are chosen for the entries of that ledger - these bookies are the initial ensemble for the ledger
* A ledger can have multiple ensembles
* Entry has only one ensemble
* Ensemble changes involve a new fragment being added to the ledger

## Write quorums
* Each entry in the log is written to write quorum bookies
* The write quorum is the subsequence of the ensemble (which is write quorum in length) starting with the bookie at index `entry ID % ensemble size` 
* Example ledger with ensemble size of `4`, write quorum of `3` and an ack quorum of `2`
  * Ensemble is `B1`, `B2` , `B3`, `B4`
  * Write quorum of the first `X` entries are
    * Entry 0: `B1`, `B2`, `B3` (first three bookies starting at index `0`)
    * Entry 1: `B2`, `B3`, `B4` (first three bookies starting at index `1`)
    * Entry 2: `B3`, `B4`, `B1` (first three bookies wrapping around to the start, at index `2`)
    * etc
* Note that the number of distinct write quorums is equal to the ensemble size
  * If the write quorum is equal to the ensemble size then there is only one distinct write quorum
* Ack quorum is any subset of the write quorum that meets the ack quorum configuration value
  * In other words, if ack quorum bookies acknowledge an entry, it has been fully replicated

## Writing to ledgers
* Bookie acknowledges a write once it has been durably persisted to disk
* Once ack quorum bookies from the write quorum acknowledge the write, the write is acknowledged to the client, but only if all entries with lower entry ids in the ledger have already been acknowledged to the client
* The written entry contains the
  * Ledger ID
  * Entry ID
  * Last add confirmed
    * This is the last entry which had been acknowledged to the client when *this* entry was written
    * This is an optimization to speed up recovery of the ledger in the case that the writer crashes
  * Payload / value
* Another client can read entries in the ledger up as far as the last add confirmed value
  * Guarantee that all entries up to and including the last add confirmed entry have been replicated on ack quorum bookies
  * Ledger needs to be opened with a non-fencing open
* If a bookie fails to acknowledge a write, the writer will create a new ensemble by replacing the failed bookie in the current ensemble
  * This creates a new fragment with this ensemble, starting from the first message that has not been acknowledged to the client
  * Creating a new fragment involves executing a check-and-set write to the ledger's metadata
  * If the check-and-set write fails, someone else has modified the ledger metadata
  * This concurrent modification could have been caused by recovery or replication
  * Metadata is read again to see if the state of the ledger is no longer `OPEN`
  * If the state of the ledger is no longer `OPEN` then the client receives an error for any outstanding writes
  * If the state of the ledger is `OPEN`, the failed node is attempted to be replaced again

## Closing a ledger as a writer
* Writer makes a check-and-set write to the ledger's metadata, changing the ledger state to `CLOSED` 
* Last entry of the ledger is set to the last entry which we have acknowledged to the client
* If the check-and-set write fails, the metadata has been modified by another actor
* Re-read the metadata and retry closing the ledger, as long as the state of the ledger is still `OPEN`
* If the state of the ledger is `IN_RECOVERY`, an error is sent to the client
* If the state of the ledger is `CLOSED` and the last entry is the same as the last entry we have acknowledged to the client, complete the close operation successfully
* If the last entry is different from what has been acknowledged to the client, send an error to the client

## Closing a ledger as a reader
* Reader can force a ledger to close
* Forcing a ledger to close will prevent any writer from adding new entries to the ledger - this is called fencing
* Fencing can occur when a writer has crashed or become unavailable and a new writer wants to take over writing to the log
* The new writer must ensure that it has seen all updates from the previous writer and prevent the previous writer from making any new updates before making any updates of its own
* To recover a ledger, update the ledger state in metadata to `IN_RECOVERY`
* Send a fence message to all bookies in the last fragment of the ledger
* When a bookie receives a fence message for a ledger, the fenced state of the ledger is persisted to disk
* When a response is received by at least `write quorum - ack quorum + 1` bookies for each write quorum in the ensemble, the ledger is fenced
* `write quorum - ack quorum + 1` bookies  ensures that if the old writer is alive and tries to add a new entry, there are no write quorums where ack quorum bookies would accept the write
* If the old writer tries to update the ensemble, the metadata check-and-set write will fail, and it will see that the ledger is currently in a `IN_RECOVERY` state
* The older writer will be able to write entries to individual bookies as there are no guarantees that the fence message will reach all bookies, but since the older writer will not be able to reach ack quorum, it will not be able to send a success response to the client
* A ledger fenced message for an entry does not mean that the entry has definitely not been written
  * It means that the entry may have been written and can only be determined after the ledger is recovered
* Recovery can only begin once a ledger is fenced
* Recovery means finding the last entry of the ledger and closing the ledger
* Finding the last entry of the ledger involves the client asking all bookies for the highest last add confirmed value they have seen
* Client waits until it has received a response from at least `write quorum - ack quorum + 1` bookies from each write quorum and takes the highest response as the entry ID to start reading from
* Then it starts reading forward in the ledger, one entry at a time, replicating all entries it sees to the entire write quorum for that entry
* Once the client cannot read any more entries, it updates the state of the ledger metadata to `CLOSED` and sets the last entry of the ledger to the last entry the client wrote

## Opening a log
* Once a bookie thinks it is a leader for a particular log it must
  * Read the list of ledgers for the log
  * Fence the last two ledgers in the ledger list
    * The last two ledgers are required because the writer may be writing to the penultimate ledger while adding the last ledger to the list
  * Create a new ledger
  * Add the new ledger to the ledger list
  * Write the new ledger to some persistence layer using a check-and-set operation
* The fencing and the check-and-set operation at prevent two bookies from thinking they have leadership at any time
* The check-and-set operation will fail if the list of ledgers has changed between reading it and writing back the new list
