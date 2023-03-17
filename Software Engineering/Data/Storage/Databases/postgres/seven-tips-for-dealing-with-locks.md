# [Seven Tips For Dealing With Locks](https://www.citusdata.com/blog/2018/02/22/seven-tips-for-dealing-with-postgres-locks/)

* Don't add a column with a default value
  * `ALTER TABLE` takes an exclusive lock that blocks reads and writes
  * Since adding a column and specifying a default value can take a while depending on the column size, this can block access to the table until the `ALTER TABLE` statement finishes successfully
* A lock queue exists where if Transaction B has a lock conflict with Transaction A, it will wait until Transaction A finishes before executing
  * If a Transaction C is executed, it will check both Transaction A and Transaction B to see if it has a lock conflict with either (and any other transaction in the lock queue)
  * Even though a transaction may execute quickly, it might be in a queue for a long time waiting for other queries to finish up and will block queries that start after it
  * When running an `ALTER TABLE` often times it's beneficial to set a lock timeout explicitly so it doesn't block queries for a very long time
* Always create indices concurrently
* Acquire aggressive locks as late in a transaction as possible to allow queries for as long as possible
  * Copying contents of a table
  * First create a new table and copy rows to new table
  * Then drop old table and rename new table
  * Make sure to explicitly take a table lock that allows writes (`EXCLUSIVE`)
* Never `VACUUM FULL` as it rewrites table to disk and blocks all queries
* Investigate deadlocks to see if can reorder commands
  * `UPDATE WHERE key = 'hello'` and `UPDATE WHERE key = 'world'` in one transaction
  * `UPDATE WHERE key = 'world'` and `UPDATE WHERE key = 'hello'` in another
  * If executed at the same time might deadlock since each grabs a lock on the other's row
  * Solved by ordering commands consistently

