# [Waiting for 9.2 - Index Only Scans](https://www.depesz.com/2011/10/08/waiting-for-9-2-index-only-scans/)

* Create an index on a table on columns `A` and `B`
  * `EXPLAIN`ing a `SELECT` `A` and `B` will output an `INDEX ONLY Scan` explanation
* Create an index on a table on column `A` and execute the same query - `EXPLAIN` will only say `INDEX Scan`
* Whenever you select data from table using index, index returns a list of tuples that _might_ be part of result
  * Some of the tuples might have been deleted, or have recently been inserted, but are not visible to the ongoing transaction
* Using a visibility map, Postgres keeps track of the newest transaction that might've changed anything about the page (`8192` bytes)
* If newest transaction that has changed page is older than transaction AND if index contains all information, can skip getting data from table
  * Index has to contain all fields used in all clauses (`SELECT`, `WHERE`, `ORDER BY`, etc.)

