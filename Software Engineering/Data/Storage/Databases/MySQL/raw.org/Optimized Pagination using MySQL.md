# [Optimized Pagination using MySQL](https://raw.org/article/optimized-pagination-using-mysql/)
## Estimating the number of rows
* When an estimation of the number of rows in a table is sufficient, a good starting point can be a `SHOW` query, which will execute quickly
* Can also use the cardinality of a column with unique elements, like an `auto_increment` column
  * `SHOW INDEX FROM CITY`
* Can utilize `information_schema` 
  * For example, if a table doesn’t receive deletes, the `auto_increment` value is the number of rows
```sql
SELECT auto_increment
FROM information_schema.tables
WHERE table_schema=DATABASE()
AND table_name='city'
```
* Table gaps can be identified using the following query
```sql
SELECT COUNT(*)/MAX(id)
FROM city
USE INDEX(PRIMARY)
```

## Get the elements
* Use a query that is based on the last ID the user saw
  * `WHERE id > or < $lastID`  depending on if `id` is ascending or descending and if the next page or previous page is being retrieved
  * This is good for next / previous pagination, but not necessarily for specific page-based pagination
* Imagine the following query, which would take a number of “buttons” to show, and each button would be assigned an offset `id` value
  * This fixed `id` offset per button approach means that behavior is consistent if a new article gets published vs. if articles were shifted one position ahead and the user would see the same article twice
```sql
SELECT id
FROM (
   SELECT id, ((@cnt:= @cnt + 1) + $perpage - 1) % $perpage cnt
   FROM news 
   JOIN (SELECT @cnt:= 0)T
   WHERE id < $last_id
   ORDER BY id DESC
   LIMIT $perpage * $buttons
)C
WHERE cnt = 0;
```