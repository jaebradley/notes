# [SQL Queries Don't Start With Select](https://jvns.ca/blog/2019/10/03/sql-queries-don-t-start-with-select/)

* Order is
  * `FROM/JOIN` + `ON`
  * `WHERE`
  * `GROUP BY`
  * `HAVING`
  * `SELECT` + window functions
  * `ORDER BY`
  * `LIMIT`
* Order is supposed to help with semantics of SQL queries
  * Can `WHERE` on something from `GROUP BY`? No, because `WHERE` happens "before" `GROUP BY`
  * Filter by something in window function? No, because window functions happen in `SELECT`, which happens after both `WHERE` and `GROUP BY`
* Aliases
  * `GROUP BY some_custom_column_name` where `some_custom_column_name` is a `CONCAT` operation can really be written like `GROUP BY CONCAT(...)`
* Queries aren't run in this order due to optimizations

