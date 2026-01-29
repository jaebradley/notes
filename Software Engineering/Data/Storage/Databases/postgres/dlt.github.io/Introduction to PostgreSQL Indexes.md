# [Introduction to PostgreSQL Indexes](https://dlt.github.io/blog/posts/introduction-to-postgresql-indexes/)
* An index will only help if less than 15-20% of the table is returned by the query, otherwise the query planner might prefer a sequential scan
  * If the query returns a large percentage of the table, consider refactoring it, using summary tables or other techniques before throwing an index at the problem

## How data is stored in disk
* Every table in Postgres has 1+ corresponding files on disk
* These files are called a "heap" and are divided into 8kb pages
* All table rows ("tuples") are saved in this files in no specific order
* Index is a tree structure that links the indexes columns to the row locators in the heap
* The directory where heap files exist can be identified by executing the `show data_directory` command
* Can use the `pg_database` and `pg_class` tables to find the file for a given table within a database
```
select oid, datname from pg_database where datname = 'my database';

select relfilenode from pg_class where relname = 'my table';
```
* The file on disk should live at `$PGDATA/base/<database oid>/<table oid>`
* Each row has a `ctid` field
  * This field is an internal field that has the address of the line in the heap
  * The value is a tuple where the first value in the tuple is the block ID and the second value in the tuple is the offset within the block
    * Can think of this as "page file index and line within that page"
* `\di+` command shows indexes in the database
  * In the example, the index has a size (\`30 MB\`) that is roughly the same size as the example table

## Memory usage
* Postgres maintains a portion of frequently accessed data and index pages in memory in shared buffers
* When an index is used, the relevant index pages are loaded into shared buffers to speed up access
* The more indexes that exist and the more they are used, these indices use additional shared buffer memory
  * Since shared buffers are limited and are used for caching data pages, filling the shared buffers with indexes can lead to less efficient caching of table data
* The whole indexed column is copied in every node of the btree
  * The larger the indexed column, the deeper the tree will be (why?)
* Postgres uses work memory (what's work memory?) when  executing queries that involve sorting or complex index scans
* Indexes require memory to store metadata about their structure, column names, statistics in the system catalog cache
* Indexes require memory for maintenance operations (vacuuming, reindexing)

## Btree index
* Balanced tree, all leaf nodes have the same distance from the root nodes
* Leaf nodes have keys and pointers to the heap
* Pointers to left/right nodes to make it easier to scan forwards and backwards

## Using multiple indexes
* Supports `AND` / `OR` conditions using bitmaps
* `where age = 30 and login_count = 100`
* Postgres scans index `age` for all pages that have `age=30` and builds a bitmap for pages that might contain rows with `age=30`
* It also will build a bitmap for the `login_count` index
* These two bitmaps are `AND`ed together to form a third bitmap
* A table scan occurs, which only reads from the pages that *might* contain candidate rows, and only adding the rows from the scanned pages that fulfill the two conditions

## Multi-column indexes
* Multi-column indexes are going to be smaller and faster than using multiple independent indexes
* Column order matters
  * Multi-column index on `a` and `b` in that order would support queries like `where a = x and b = y` and `where a = x` but not `where b = y`
