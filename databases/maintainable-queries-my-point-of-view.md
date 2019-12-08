# [Maintainable Queries - My Point of View](https://www.depesz.com/2009/01/04/maintainable-queries-my-point-of-view/)

* Priorities for writing maintainable queries
  * Avoid useless typing
  * ALWAYS use aliases for tables/views and make them sensible aliases
  * Indent code
  * Avoid quotations if possible
  * Use join syntax
* Long, repeated names make it more difficult to read
* Repeated expressions make it easier to make mistakes when maintaining / replacing (changing "+1" to "+2" in only a subset of places in query)
* When joining a table, it can become unclear what columns that are `SELECT`ed are from which table
  * Will cause more overhead for next person, especially if they are unfamiliar with tables
  * So alias ALL columns - this makes it way easier to immediately see which columns are from what tables (even if the columns are unique for a given table)
* The avoid quotations point is because putting quotes around names makes them case-sensitive
  * So by avoiding quotes the name is now case-insensitive which is generally desired
  * For what it's worth it also adds keystrokes and generally indicates that quotes are used for a specific purpose (some non-standard characters)

```sql
SELECT
  # some fields
FROM
  table1,
  table2
WHERE
  field1 = 'something'
  AND table1_id = table2_id
```

* The above syntax is inspired by Django
  * Django popularized using `WHERE` filter to specify table join criteria
  * Makes it unclear what conditions are used for join and data filtering
  * If ever want to change type of join, need to modify `FROM` and `WHERE`
