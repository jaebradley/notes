# 6.9.4.2 Locking Protocol

* If a transaction has obtained a shared-mode lock on an an item, the transaction can read the item, but not write the item
* If a transaction has obtained an exclusive-mode lock on an item, the transaction can read and write the item
* If an item already has an exclusive lock on it, then if a transaction needs to read or write that item, the transaction will have to wait until the exclusive lock is released
* Two-phase locking protocol ensures serializability
  * Growing phase: transaction obtains locks, but will not release any locks
  * Shrinking phase: transaction may release locks, but may not obtain any new locks
  * Does not ensure freedom from deadlocks
