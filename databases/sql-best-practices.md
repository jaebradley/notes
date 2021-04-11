# [SQL Best Practices](https://www.metabase.com/learn/building-analytics/sql-templates/sql-best-practices)

* Although it's possible to join using a `WHERE` clause (also called an implicit join) prefer an explicit `JOIN` with an `ON` keyword
* Filter with `WHERE` before `HAVING`
* Placing a function in a `WHERE` prevents the database from using an index on a column (forces a full-table scan, which runs the function on each row of the table)
  * Concatenation operator is also a function
* Avoid adding wildcards to the beginning of a string - can lead to a full table scan
* `EXISTS` exits as soon as it finds the search value whereas `IN` will scan the entire table
* Prefer grouping by columns with more unique values first before grouping by columns with fewer distinct values (so group by `id` first then group by `state`)
* `UNION ALL` does not remove duplicates so it will be more efficient
* Partial indices can also be useful for date ranges, for example, indexing the past week of data only
* Use composite indices for columns that typically are queried together like first name & last name

