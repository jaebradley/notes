# [PostgreSQL Gotchas](https://www.depesz.com/2007/09/06/postgresql-gotchas/)

* Don't use the `!` operator - it is *not* `NOT` but rather the _factorial_ operator
  * `SELECT * FROM x WHERE i ! IN (3, 5)` => `SELECT * FROM x WHERE (i!) IN (3, 5)`
* Always quote dates i.e. `2007-01-01  is not a date, it's an arithmetic formula that gets translated to `2007 - 1 - 1` or `2005`
  * This is resolved in `8.3+`
* Scenario is a `text1` and a `text2` column in two separate tables
  * They have some values in common
  * Query is `SELECT * FROM first WHERE text1 IN (SELECT text1 FROM second)`
  * Result is all rows in `first` because the correlated sub-query will look at [the tables in the main query (in this case, `first`)](https://stackoverflow.com/questions/20603404/in-subquerys-where-condition-affects-main-query-is-this-a-feature-or-a-bug) if it cannot find the column name in the tables in the correlated sub-query
  * In other words, `text1` doesn't exist in `second` so the correlated subquery will get the results from the main query for the `text1` column
  * Solution is to name columns more specifically and to make sure to always alias tables
