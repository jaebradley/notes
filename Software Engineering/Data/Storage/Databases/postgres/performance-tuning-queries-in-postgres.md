# [Performance Tuning Queries in PostgreSQL](https://www.geekytidbits.com/performance-tuning-postgres/)

* `pg_stats_statements` module tracks execution stats of SQL statements to find poor performing queries
* `auto_explain` module also finds slow queries, but it logs execution plan and supports loggin nested statements
  * Nested statements are those executed inside a function

## Indices

* Find tables that have more sequential scans than index scans which is good sign that an index will help

```sql
SELECT
  relname,
  seq_scan - idx_scan AS too_much_seq,
  CASE
    WHEN
      seq_scan - coalesce(idx_scan, 0) > 0
    THEN
      'Missing Index?'
    ELSE
      'OK'
  END,
  pg_relation_size(relname::regclass) AS rel_size, seq_scan, idx_scan
FROM
  pg_stat_all_tables
WHERE
  schemaname = 'public'
  AND pg_relation_size(relname::regclass) > 80000
ORDER BY
  too_much_seq DESC;
```

* Find unused indices

```sql
SELECT
  indexrelid::regclass as index,
  relid::regclass as table,
  'DROP INDEX ' || indexrelid::regclass || ';' as drop_statement
FROM
  pg_stat_user_indexes
  JOIN
    pg_index USING (indexrelid)
WHERE
  idx_scan = 0
  AND indisunique is false;
```

## Environmental Factors

* As much as possible use prod and not dev environment
  * When a machine has less memory, PostgreSQL may not be able to perform a Hash Join
  * If the number of table rows is small, PostgreSQL may perform a sequential scan on the table rather than an available index

## EXPLAIN ANALYZE

* Each indented block with a -> is a node
  * Node is logical step with a cost and execution time
  * Statistics are a rollup of child nodes
* Cost values are made up of two numbers
  * First is start up cost - cost to retrieve first record
  * Second is cast incurred to process entire node
  * How much work PostgreSQL estimates it will have to do to run statement
  * Value is not how much time is required
* Actual time
  * In milliseconds
  * First number is start up time
  * Time taken to process entire node
