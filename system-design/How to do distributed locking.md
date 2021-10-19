# [How to do distributed locking](https://martin.kleppmann.com/2016/02/08/how-to-do-distributed-locking.html)

* Two reasons to use a lock in a distributed application - for efficiency or for correctness
* Taking a lock saves you from unnecessarily doing the same work twice
    * If the lock fails, two nodes end up doing the same piece of work (minor incrase in cost) or the user ends up getting the same email notification twice (minor inconvenience)
  * Taking a lock prevents concurrent processes from stepping on each others' toes and messing up the state of your system
    * If the lock fails and two nodes concurrently work on the same piece of data, the result is a corrupted file, data loss

## Protecting a resource with a lock

* A lock in a distributed system is not like a mutex in a mult-threaded application
* Application where client needs to update a file in shared storage
  * Client first acquires the lock, reads the file, makes some changes, writes the modified file back, and releases the lock

```javascript
// THIS CODE IS BROKEN
function writeData(filename, data) {
    var lock = lockService.acquireLock(filename);
    if (!lock) {
        throw 'Failed to acquire lock';
    }

    try {
        var file = storage.readFile(filename);
        var updated = updateContents(file, data);
        storage.writeFile(filename, updated);
    } finally {
        lock.release();
    }
}
```

* Corrupted data could occur if the client acquiring the lock is paused for an extended period of time while holding the lock due to GC, for example
* Lock has a timeout / it is leased, which is always a good idea, or else a crashed client could end up holding a lock forever and never release it
* If GC pause lasts longer than the lease expiry period, the client doesn't realise that it has expired, it may go ahead and make some unsafe changes
* Stop-the-world GC pauses have sometimes been known to last for several minutes
  * Even "concurrent" garbage collectors cannot fully run in parallel with application code - even they need to stop the world from time to time
* GC can pause a running thread at any point
* Your processes will get stopped

## Making the lock safe with fencing

* Include a fencing token with every write request to the storage service
* Client 1 acquires the lease and gets a token of 33, but then goes into a long pause and the lease expires
* Client 2 acquires the lease, gets a token of 34, and then sends a write to the storage service including the token
* Client 1 unpauses and attempts to write to the storage service, but since it's token value is lower than the last seen by the storage service, the storage service rejects the write request
* Fencing token must have strictly monotonically increasing tokens
