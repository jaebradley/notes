# [Index-only Scans](https://wiki.postgresql.org/wiki/Index-only_scans)

* Btree indexes contain what are technically redundant copies of the column data that's indexed
* PostgreSQL indexes do not contain visibility information
  * It is not directly possible to ascertain if any given tuple is visible to the current transaction

## Visibility Map

* A data structure associated with a table that tracks which tuples are visible to all transactions
  * Tuples from a transaction may or may not be visible given another transaction dependning on if the transaction was committed (or if the transaction aborted)
  * There is one bit for each page in a table
  * Facilitiates VACUUM
    * A set bit indicates all tuples on page are known to be visible to all transactions and vacuuming page is unnecesary

## What types of queries may be satisfied by an index-only scan

* The need to visit the heap where all tuples are not known to be visible is relatively expensive
* Planner considers heap visits when weighing to use index-only scan - the need to ensure the bulk of the table's tuples have their visibility map bits set is likely to restrict index-only scans' usefulness to queries against infrequently updated tables
  * Not necessary for all bits to be set - "index-mostly scans" is most accurate name
