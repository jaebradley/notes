# [Explaining the Unexplainable](https://www.depesz.com/2013/04/16/explaining-the-unexplainable/)

* Sequentially scanning table might not always be faster than index
  * If table has one row
    * Read page from index, check if row matching condition in table, and then read page from table
  * Even for tables that have 10 billion rows and each column value is same, index doesn't help
* Postgres examines all plans given the information it knows (total row count, number of rows that will match)
* Postgres "cost" unit is "fetching single page in sequential manner"
  * Can set parameters in `postgresql.conf` to tune the various costs of reading sequential page
* First cost number is the cost for starting the operation row and the second number is the cost for getting all rows
  * For sequential scan there is no starting cost - read page and return rows
  * But if you need to sort dataset, need to read all data first, sort it, and only then start returning rows
* Width is the average length, in bytes, of a row returned by the operation
* If there's a function that returns 3 rows, and sleeps for 1 second before returning rows, calling `SELECT` on the function should take `3` seconds
* But `SELECT` with a `LIMIT` of `1` will also take `3` seconds because Postgres cannot return partial results
  * All results are stored in buffer and returned together when function execution ends
* Sequential scan on a table _can_ return partial data
* `EXPLAIN ANALYZE` will return the actual time it took to run a query (time it took to return first row, time it took to return all rows)
  * Also will return how many rows (on average) an operation returned
  * And how many times an operation ran (loops)
