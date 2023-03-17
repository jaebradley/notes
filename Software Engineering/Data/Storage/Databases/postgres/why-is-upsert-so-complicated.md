# [Why is UPSERT so complicated](https://www.depesz.com/2012/06/10/why-is-upsert-so-complicated/)

* What makes upsert so complicated, it should never raise exception
* Simplest solution is to execute the update statement and if it fails run an insert statement
  * Will run into race conditions where two identical upserts execute the update statement, both fail (because they cannot find the row in question) and both execute insert statements, except the second insert will fail
  * Transaction doesn't help because transactions in the default _isolation level_ just make sure that other connections do not see the changes in the transaction before they are committed and to rollback / abandon your changes
* What about using a table lock (since cannot lock row if it doesn't exist)?
  * `LOCK TABLE x IN ACCESS EXCLUSIVE MODE`
  * Will work but major performance penalties
* Attempt to use advisory lock, which means that you're applying lock on a number (and not a row or table)
* Using a savepoint, which is a point in a transaction that can be rolled back to, even in case of errors that happened after setting savepoint
  * Do update and if it worked, commit transaction
  * If update failed, create a savepoint and attempt insert
  * If insert failed because row already exists, rollback to savepoint and attempt update
* Savepoint approach is susceptible to race-condition around deletes, where when insert fails because row already exists, when the update is attempted, a delete of that same row has occurred so the resultant update fails
  * Chances are obviously low, but technically possible
  * So then add a loop that continues to attempt the entire insert/update process and potentially make sure that it cannot be infinite loop if things were to run in perfect synchronization
