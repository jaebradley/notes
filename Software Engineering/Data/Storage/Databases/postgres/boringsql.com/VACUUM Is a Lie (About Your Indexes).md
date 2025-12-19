# [VACUUM Is a Lie (About Your Indexes)](https://boringsql.com/posts/vacuum-is-lie/)
* A deleted row in Postgres is marked as a "dead" tuple
* Invisible to new transactions but physically present
* Only when all transactions referencing the row are finished does `VACUUM` come along and actually remove the rows, reclaiming the table space
* Table data lives in a collection of 8 KB pages
* Rows are stored wherever they fit and there is no inherent order
* When inserting a row, Postgres finds a page with enough free space and writes the row to the page
* `VACUUM` removes dead tuples and compacts the remaining rows within each page
* Postgres can only reclaim an entire page when it becomes empty
* Indices are also stored on 8 KB pages but must maintain order
* `VACUUM` does not restructure b-trees because the ordering of the rows within a page matters
  * This is not the same for non-indexed rows, which can be moved anywhere
  * `VACUUM` removes the dead tuple pointers from index pages
  * Marks completely empty pages as reusable
  * Updates the free space map
* `VACUUM` does not 
  * Merge sparse pages together
  * Reduce index tree depth
  * Deallocate empty-but-still-linked pages
  * Change the physical structure of the B-tree
* Author's example of inserting many rows into a table with an index
  * Author removed the middle 80% of the rows
  * Table shrunk in size while the indices remained unchanged in size
  * `VACUUM` doesn't return space to the operating system, it only marks pages as reusable within Postgres
* Aim for leaf pages in an index to have 80%+ density
* Default `fillfactor` value is 90%
  * Controls how tightly Postgres packs pages during data storage
  * A lower `fillfactor` leaves more room in the page, which can reduce page splits when inserting into the middle of an index
    * This is useful for tables with random index column inserts or those with heavily updated index columns
* Querying `relpages` from `pg_class` returns the physical file size divided by the 8 KB page sizes
  * Reflects the actual file on disk and not how much useful data is inside
  * So index file can still allocate a non-trivial amount of disk space, even if most pages are empty
* The simplest way to spot index bloat is to compare actual size to expected size
  * A bloat ratio above 2 deserves investigation
* `pgstatindex` physically reads the entire index, which will impact performance on product servers

```
SELECT
    c.relname as index_name,
    pg_size_pretty(pg_relation_size(c.oid)) as actual_size,
    pg_size_pretty((c.reltuples * 40)::bigint) as expected_size,
    round((pg_relation_size(c.oid) / nullif(c.reltuples * 40, 0))::numeric, 1) as bloat_ratio
FROM pg_class c
JOIN pg_index i ON c.oid = i.indexrelid
WHERE c.relkind = 'i' 
  AND c.reltuples > 0
  AND c.relname NOT LIKE 'pg_%'
  AND pg_relation_size(c.oid) > 1024 * 1024  -- only indexes > 1 MB
ORDER BY bloat_ratio DESC NULLS LAST;
```

* `REINDEX` rebuilds the index from scratch
  * Use `CONCURRENTLY` to avoid `ACCESS EXCLUSIVE` locks
* `VACUUM FULL` rewrites tables and indices but requires an `ACCESS EXCLUSIVE` lock, which will block reads and writes for the entire duration
