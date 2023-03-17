# [Understanding Postgres Performance](http://www.craigkerstiens.com/2012/10/01/understanding-postgres-performance/)

## Cache + Hit Rate

* Generally, a good cache hit rate is 99%

```sql
SELECT
  SUM(heap_blks_read) AS heap_read,
  SUM(heap_blks_hit) AS heap_hit,
  SUM(heap_blks_hit) / (SUM(heap_blks_hit) + SUM(heap_blks_read)) AS cache_rate
FROM
  pg_statio_user_tables;
```

* Query to generate list of tables in the database with the largest ones first, and the percentage of the time which they use an index

```sql
SELECT
  relname,
  100 * idx_scan / (seq_scan + idx_scan) index_usage_rate,
  n_live_tup_rows_in_table
FROM
  pg_stat_user_tables
WHERE
  seq_scan + idx_scan > 0
ORDER BY
  n_live_tup DESC;
```

* Should be around 99% of the queries use an index on any table over 10k rows
