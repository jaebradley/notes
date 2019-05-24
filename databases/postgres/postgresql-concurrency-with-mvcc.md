# [PostgreSQL Concurrency with MVCC](https://devcenter.heroku.com/articles/postgresql-concurrency)

* Every transaction in Postgres gets a transaction id
  * Includes inserts, updates, or deletes as well as `BEGIN/COMMIT` statements
* Every transaction increments the global transaction id and assigns value to current transaction
* Transaction data is stored on every row in Postgres - this is how Postgres knows what records are "visible" to a given transaction
* Inserting a row will store a transaction id on the row on a property called `xmin`
  * Every row that has been committed and has an `xmin` that is less than the current transaction's id is visible to the transaction
  * Also note that the row must have been committed so a row can be committed as part of a transaction but until that row has been committed it won't be visible to other transactions

## [`xmax` and `DELETE`s / `UPDATE`s](https://stackoverflow.com/questions/39058213/postgresql-upsert-differentiate-inserted-and-updated-rows-using-system-columns-x)

* When a row is updated or deleted, Postgres creates a new version of the row (each version is also called a tuple)
* The old version of the row will be deleted by `autovaccuum` when it is no longer needed
* `xmax` can be the transaction id of the transaction that updated or deleted a tuple
  * Transactions that have a transaction id > the tuple's `xmin` but < the tuple's `xmax` can see that tuple
  * A tuple can be deleted safely if there's no transaction with an id less than `xmax`
* `xmax` also stores any row locks (i.e. the ids of the transactions that are locking the row)

## Two Transactions Updating the Same Row at the Same Time

* Postgres uses transaction isolation levels, like `READ COMMITTED`
* `READ COMMITTED` reads the row after the first transaction has completed and then executes the second transaction
* Basically, it starts over if rows changed while it was waiting
* `UPDATE` + `WHERE` in a second transaction will run again after the initial transaction commits

```bash
BEGIN with TXID 1 ---

UPDATE SET value = 2  ------------ BEGIN with TXID 2
WHERE value = 1;
                                   SET TRANSACTION ISOLATION READ COMMITTED
COMMIT (affects 1 row)             UPDATE SET value = 3
                                   WHERE value = 1;

                     ------------- COMMIT (affects 0 rows)
```
* Bunch of other transaction isolation levels like `SERIALIZABLE` which will just fail if the row has been modified

## Vacuuming

* Vacuuming is important because updates and deletes create new versions of the row
* Dead rows (i.e. versions of a rows that are no longer being referenced by any transactions) need to be removed
* Transaction IDs are 32 bits and can only support 4 billion transaction IDs before wrapping around to 0
* Wrapping around could cause unexpected rows to appear in transactions
