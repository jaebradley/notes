# [PostgrSQL At Scale: Database SChema Changes Without Downtime](https://medium.com/braintree-product-technology/postgresql-at-scale-database-schema-changes-without-downtime-20d3749ed680)

* Because locks on multiple tables creates the possibility of deadlock, DDL statements are not combined in a single transaction
  * Postgres disallows executing `CREATE INDEX CONCURRENTLY` inside an explicit transaction; instead Postgres manages the transaction
* Postgres table-level locks
  * `ACCESS EXCLUSIVE` - blocks all usages of the locked table
  * `SHARE ROW EXCLUSIVE` - blocks concurrent DDL against the table and row modification (allowing reads)
  * `SHARE UPDTE EXCLUSIVE` - blocks concurrent DDL against the locked table
  * All DDL operations generally requires _some_ type of lock to be established against the specified table
  * For example, `ALTER TABLE` will lead to an `ACCESS EXCLUSIVE` lock being established on the table
    * This will cause all subsequent queries on the table to queue until the lock is released
    * Arbitrarily long queueing of incoming queries is indistinguishable from an outage, so avoid long-running queries
  * Manual lock acquisition is preferred vs. relying on Postgres to lock an object
    * the `pg_locks` table is queried before attempting to acquire a lock to see if there are any long-running queries to avoid unnecessarily queueing queries for several seconds when it is unlikely that lock acquisition would succeed
  * An `ACCESS_EXCLUSIVE` lock is often times only needed for a short period of time (i.e. the amount of time it takes Postgres to update it's "catalog" / metadata tables)

## Table Creation

* Including a foreign key in the table definition will cause Postgres to acquire a `SHARE ROW EXCLUSIVE` lock against the referenced table, which will block any concurrent DDL or row modifications
* Prefer to split the table creation and then add the foreign key

## Table Deletion

* Dropping a table requires an exclusive lock on the table
* Dropping a table requires documentation showing that all references to the table were removed from the codebase

## Table Renaming

* Should be avoided at all costs, but if unavoidable, a safe approach might be
  * Create a new table with the same schema as the old one
  * Backfill the new table, with a copy of the data as the old table
  * Use `INSERT` and `UPDATE` triggers on the table to maintain parity in the new table
  * Begin using the new table (and drop the old table)

## Column Addition

* Introducing a default value at the same time as adding a column will cause the table to be locked while the default value is propagated for all rows in the table
  * Add the new column (without a default value)
  * Set the default value on the column
  * Backfill all existing rows separately
* In Postgres 11, reads of rows without a value for the new column, will have that value magically filled on the fly
* Adding a column will cause all `SELECT *` queries to begin returning the new column - to avoid having to ensure all existing code handle these new columns safely, avoid `*` expansion in favor of explicit column references

## Column Type Changing

* Add a new column, and then dual write to both columns using a `BEFORE INSERT/UPDATE` trigger
* Backfill new column with copy of old column's values
* Rename current column to some deprecated column name and rename the new column to the previous column name inside a single transaction with an explicit `LOCK` table statement
* Drop the old column

## Column Removal

* Dropping a column requires an exclusive lock on the table to update the metadata table, but does _not_ rewrite the table
* Any indices using the column should be dropped separately with `DROP INDEX CONCURRENTLY` otherwise they will be automatically dropped along with the column under an `ACCESS EXCLUSIVE` lock
  * Can query `pg_depend` to see data around dependent objects

## Index Creation

* `CREATE INDEX` acquires an `ACCESS EXCLUSIVE` lock to build the index using a single table scan
* `CREATE INDEX CONCURRENTLY` acquires a `SHARE UPDATE EXCLUSIVE` lock, but must complete two table scans (so is operationally slower)
  * However, it allows for reads and writes while building the index
* Multiple concurrent index creation on a single table will not return from any `CREATE INDEX CONCURRENTLY` statement until the slowest one completes
* `CREATE INDEX CONCURRENTLY` cannot be executed inside of a transaction, but does maintain transactions internally
  * Because a transaction is open as the index is being built, no auto-vacuums will cleanup dead tuples until the index creation has finished
  * Must wait for all transactions using the table to finish before returning

## NOT NULL Constraint

* Removing an existing not-null constraint from a column requires an exclusive lock on the table while a metadata table update is performed
* Adding a not-null constraint on an existing column requires an exclusive lock on the table while a full table scan verifies that no `null` values exist
  * Instead, add a `CHECK` constraint that requires the column be non-null like `ALTER TABLE ... ADD CONSTRAINT ... CHECK (... IS NOT NULL) NOT VALID`
  * The `NOT VALID` tells Postgres to stop scanning if it encounters a row that does not satisfy the condition
  * Validate the constraint, as Postgres will block acquisition of other EXCLUSIVE locks on the table, but will not block reads/writes

## Foreign Keys

* `Adding a foreign key will require a `SHARE ROW EXCLUSIVE` lock on both the altered table and the referenced table
* `ALTER TABLE ... ADD FOREIGN KEY ... NOT VALID` will add the foreign key and enforce the constraint for all newly inserted or updated statements - breifly holds `SHARE ROW EXCLUSIVE` locks
* `ALTER TABLE ... VALIDATE CONSTRAINT` - checks that all existing rows conform to the specified constraint
  * Requires a `SHARE UPDATE EXCLUSIVE` lock so may run concurrently with row-reading and modifying queries
