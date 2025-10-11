# [Transaction Isolation in Postgres, explained](https://www.thenile.dev/blog/transaction-isolation-postgres)
* The ideal isolation (serializable) occurs when there are multiple concurrent transactions and the resulting state of the database is as if these transactions ran sequentially

## Isolation levels and their anomalies

### Dirty Reads
* Reading data that has not yet been committed
* If a transaction is not committed yet it may never get committed (aborted, rolled back)
* Mostly useful in write-heavy workloads and where read accuracy is not important
* Because this isolation level breaks most basic transaction expectations many databases including Postgres do not support this isolation level

### Non-repeatable Reads
* `read committed` isolation level allows you to see changes that were committed before the start of the transaction
  * Default isolation level in Postgres
* Means that the same query within one transaction can return different results if a parallel session committed in-between
* Issues occur when executing logic that is "write after read"

<img width="1884" height="936" alt="Screenshot 2025-10-11 at 11 50 15@2x" src="https://github.com/user-attachments/assets/ff19288b-efe7-4362-896d-648d0456a7d2" />

* So statements like `if query returns values > X, update those query rows to be Y`
  * If the queried rows change between the read (i.e. query execution) and the actual row update, data that shouldn't have been updated will be updated
  * Result is a state that would not have happened if the sessions were executed serially
  * For the above example, use an `UPDATE + WHERE` to execute the update in a single statement
  * However, if the `where` runs a query like (`WHERE value in (SELECT values FROM table)`) this is another flavor of "write after read"
  * Can lock rows returned by sub-`SELECT` in `where` via `SELECT...FOR UPDATE` so other sessions won't be able to update the balance in any account while the update is in progress
  * Classic pattern of implementing job queues in Postgres uses `FOR UPDATE` to prevent multiple workers from picking the same job
 
<img width="1872" height="402" alt="Screenshot 2025-10-11 at 11 51 49@2x" src="https://github.com/user-attachments/assets/05d31f06-a838-4c72-a730-62eae3e5fd0e" />

### Phantom reads
* Isolation level "repeatable reads"
* New rows that are committed by concurrent sessions can be read midway through current transaction

<img width="1880" height="802" alt="Screenshot 2025-10-11 at 11 50 36@2x" src="https://github.com/user-attachments/assets/80b60cd3-0ee1-4603-a0d0-0020383647cb" />

* Two forms of inconsistency that can happen 
  * The same `SELECT` can return new rows (i.e. different results)
  * Query can return data that was `INSERT`ed but not `UPDATE`d
    * ...unless the update causes the row to match a query
* Databases implemented repeatable reads isolation level by locking the rows returned by the query in the transaction
* Doesn't block readers, but does delay writers until the transaction committed
* Since it's not possible to lock rows that don't exist yet, that's why the new "phantom" rows showed up

## MVCC and isolation in Postgres
* When a transaction first writes to the database, it is assigned a sequence number (transaction ID)
* When a transaction modifies rows, the original version of the row is preserved and the new version is added with the transaction ID
  * Can see the current transaction ID via `select txid_current()`
* Each transaction only sees the versions of each row that is earlier than its transaction ID
* Each transaction takes a snapshot of the currently active transactions (`select * from pg_current_snapshot()`)
* The "history" of a row (i.e. the multiple version of a row) have a `t_xmin` value associated with it (the transaction ID of the transaction that created/modified the row) 
  * This is how transactions identify the "correct" data for a given row given the transaction's current transaction ID
 
<img width="1852" height="454" alt="Screenshot 2025-10-11 at 11 51 05@2x" src="https://github.com/user-attachments/assets/6cbc0efd-1c52-4ead-875b-6b74c86b2c2f" />

* Freezing process takes rows that were last modified by the oldest transactions and indicate that they should always be visible
  * Frees up transaction IDs for reuse

## Performance implication of serializable in Postgres
* `Could not serialize due to concurrent update` errors can occur in the serializable transaction level when there are two concurrent transactions that attempt to update the same row
* One of the sessions attempts to update a value without having seen its latest version

<img width="2702" height="892" alt="Screenshot 2025-10-11 at 11 52 12@2x" src="https://github.com/user-attachments/assets/97bd1f77-775d-42d9-8d65-3ef5be2466a7" />
