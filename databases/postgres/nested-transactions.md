# [Nested Transactions](https://stackoverflow.com/a/33860302/10039741)

* Postgres does not have a traditional concept of a nested transaction
  * Common understanding of nested transaction is that it can be committed even when outer transaction is rolled back
* Postgres has `SAVEPOINT`s but these get rolled back if outer transaction gets rolled back
  * Transactions with nested partial points
  * Create `SAVEPOINT`s with **unique names**
  * `RELEASE SAVEPOINT X` commits the savepoint though savepoint only persists if wrapping transaction commits
  * `ROLLBACK TO SAVEPOINT X` does what it suggests
  * Releasing or rolling back savepoints automatically releases all savepoints defined after it
* [**PostgreSQL actually treats every SQL statement as being executed within a transaction. If you do not issue a BEGIN command, then each individual statement has an implicit BEGIN and (if successful) COMMIT wrapped around it. A group of statements surrounded by BEGIN and COMMIT is sometimes called a transaction block.**](https://www.postgresql.org/docs/10/tutorial-transactions.html)
