# [Reading a Postgres EXPLAIN ANALYZE Query Plan](https://robots.thoughtbot.com/reading-an-explain-analyze-query-plan)

* Example query plan

```text
EXPLAIN ANALYZE SELECT *
FROM tenk1 t1, tenk2 t2
WHERE t1.unique1 < 100 AND t1.unique2 = t2.unique2 ORDER BY t1.fivethous;

                                                                 QUERY PLAN
--------------------------------------------------------------------------------------------------------------------------------------------
 Sort  (cost=717.34..717.59 rows=101 width=488) (actual time=7.761..7.774 rows=100 loops=1)
   Sort Key: t1.fivethous
   Sort Method: quicksort  Memory: 77kB
   ->  Hash Join  (cost=230.47..713.98 rows=101 width=488) (actual time=0.711..7.427 rows=100 loops=1)
         Hash Cond: (t2.unique2 = t1.unique2)
         ->  Seq Scan on tenk2 t2  (cost=0.00..445.00 rows=10000 width=244) (actual time=0.007..2.583 rows=10000 loops=1)
         ->  Hash  (cost=229.20..229.20 rows=101 width=244) (actual time=0.659..0.659 rows=100 loops=1)
               Buckets: 1024  Batches: 1  Memory Usage: 28kB
               ->  Bitmap Heap Scan on tenk1 t1  (cost=5.07..229.20 rows=101 width=244) (actual time=0.080..0.526 rows=100 loops=1)
                     Recheck Cond: (unique1 < 100)
                     ->  Bitmap Index Scan on tenk1_unique1  (cost=0.00..5.04 rows=101 width=0) (actual time=0.049..0.049 rows=100 loops=1)
                           Index Cond: (unique1 < 100)
 Planning time: 0.194 ms
 Execution time: 8.008 ms
```

* `loops=` means that the index scan was executed 1 time
* `Bitmap Index Scan` + `Bitmap Heap Scan` is much more expensive than reading the rows sequentially (`Seq Scan`) but because relatively few rows need to be visited, the two step process ends up being faster
  * Further sped up by sorting the rows into physical order before fetching them, minizing the costs of separate fetches
* Result of heap scan, rows from `tenk1` for which `unique1 < 100` are inserted into in=memory Hash table - this takes no time at all
* Hash node has info about hash buckets and batches, as well as peak memory usage
  * When batches > 1 there is disk usage, but not shown
  * Memory usage is probably memory taken by Hash keys
* Postgres reads all 10,000 rows from `tenk2` and checks them against Hash of `tenk1 ` rows
  * `Hash Join` means that the rows of one table are entered into an in-memory hash
    * After which the rows of another table is scanned and its values probed against the hash table for matches
    * The match condition is `Hash Cond: (t2.unique2 = t1.unique2)`
    * Since values from both `tenk1` and `tenk2` are selected, the width of each row doubles during the `Hash Join`
* Sort result set by `Sort Key: t1.fivethous`
  * `quicksort` algorithm is used
  * whether sort is in-memory or on disk (this can impact speed)
  * and amount of memory necessary
