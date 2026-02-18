# [The Part of PostgreSQL We Hate the Most](https://www.cs.cmu.edu/~pavlo/blog/2023/04/the-part-of-postgresql-we-hate-the-most.html)
* The goal of Multi-Version Concurrency Control is to allow multiple queries to read and write to the database simultaneously without interfering with each other, when possible
* Basic idea of MVCC is that the DBMS never overwrites existing rows
* For each logical row, the DBMS maintains multiple physical versions
* When serving a database query, the DBMS determines which version to retrieve to satisfy the request according to some version ordering
* Multiple queries can read older versions of rows without getting blocked by another operation updating that row
* Queries observe a snapshot of the database as it existed when the DBMS started that query's transaction
* This approach eliminates the need for explicit record locks that block readers from accessing data while writers modify the same item

### Example table schema

```sql
CREATE TABLE movies (
  id INTEGER PRIMARY KEY GENERATED ALWAYS AS IDENTITY,
  name VARCHAR(256) NOT NULL,
  year SMALLINT NOT NULL,
  director VARCHAR(128)
);
CREATE INDEX idx_name ON movies (name);
CREATE INDEX idx_director ON movies (director);
```

* The core idea of PostgreSQL's MVCC scheme is that when a query updates an existing row in a table, the DBMS copies that row and applies the changes to this new version instead of overwriting the original row
  * This is known as an append-only version storage scheme
* With two physical versions representing the same logical row, the DBMS needs to record the lineage of these versions
* MVCC DBMs achieve this by creating a version chain via a singly linked-list
  * This singly-linked list can be ordered newest-to-oldest or oldest-to-newest
  * For the N2O approach, each tuple version points to the previous version and the version chain's head is always the latest version
  * For the O2N approach, each tuple version points to the newer version, and the head is the oldest tuple version
    * This approach avoids needing to update indexes to point to a newer version of the tuple each time it's modified
    * May take the DBMS longer to find the latest version of a row when processing queries as the DBMS may need to traverse a long version chain
  * Most DBMSs, including Oracle and MySQL, implement N2O, however Postgres implements O2N
  * The header for each Postgres row contains a tuple ID field (\`t_tcid\`) of the next version
    * This tuple ID will be its own tuple ID if it is the latest version
  * Postgres developers realized that there were two problems with this strategy
    * Making a copy of an entire tuple every time it is updated is expensive
    * Traversing the entire version chain to find the latest version, which is what most queries want, is wasteful
  * To avoid traversing the entire version chain, Postgres adds an entry to a table's indexes for each physical version of a row
    * So if there are five physical versions of a logical row, there will be, at most, five entries for that tuple in the index
  * So in the movie table index example, the `idx_name` index will contain entries for the previous "Movie Name" rows, which may be on separate pages on disk
    * This allows directly looking up the latest version of a tuple without needing to traverse the version chain
  * Postgres tries to avoid having to install multiple index entries and storing related versions over multiple pages by creating a new copy in the same disk page as the old version to reduce disk I/O
    * Heap-only tuple updates
  * The DBMS uses the heap-only tuple update approach if an update does not modify any columns referenced by a table's indexes and the new version is stored on the same data page as the older version and if there is space on that page

## Version Vacuum
* The vacuum procedure performs a sequential scan on table pages modified since its last run and finds expired versions
* Expired versions are row versions that are not visible to any active transaction
* In other words, no current transaction is accessing the row version and future transactions will use the latest row version(s) instead

## Problem #1: Version Copying
* If an operation updates a tuple, the DBMS copies all its column values into a new version
* This occurs whether or not the query updates a single column or multiple columns
* This leads to a lot of data duplication and increased storage requirements, especially relative to other databases
* MySQL and Oracle will storage a compact delta between the new and current versions
* So if an operation only updates a single column, then the DBMS will store a delta record for that single column change whereas Postgres would create a new version with duplicated values aside from the single column value change

## Problem #2: Table Bloat
* Write-heavy workloads may accumulate dead tuples faster than auto-vacuuming can catch up, leading to database growth
* DBMS has to load dead tuples into memory during query execution since dead tuples and live tuples are intermingled on disk pages
* These dead tuples will impact query performance and cause the DBMS to incur more operations and consume more memory than absolutely necessary
* Example: movies table has 10 million live and 40 million dead tuples (80% of the table has obsolete data)
  * Let's assume the table has many columns and the average size of a tuple is 1 KB
  * Live tuples occupy 10 GB of storage space while the dead tuples occupy \~40 GB of storage space for a total size of 50 GB
  * When a query performs a full table scan, Postgres must retrieve all 50 GB from disk and store it in memory
* Autovacuum only removes dead tuples and relocates live tuples within each page, but does not reclaim empty pages from disk
* So even if Postgres removed 40 GB of dead tuples from the movies table, it will still retain 50 GB of allocated disk space
  * Must use `VACUUM FULL` to rewrite the entire table to a new space with no wasted storage

## Problem #3: Secondary Index Maintenance
* A single update to a tuple requires Postgres to update all indexes for that table
  * This is because Postgres uses the exact physical location of a version in both primary and secondary indexes
* In the basic `UPDATE` statement example, Postgres creates a new version of the row by copying the original version into a new page
  * Inserts entries pointing to the new version in the table's primary key index and secondary indexes
* Postgres avoids updating indexes each time if it can perform a heap-only tuple write where the new version is on the same page as the current version
  * Roughly 46% of updates use the heap-only tuple write optimization - however, this also means that more than 50% of all updates lead to an index update
* Oracle and MySQL do not have this problem because their secondary indexes do not store the physical addresses of new versions
  * Instead, they store logical identifiers like a tuple ID and a primary key that the DBMS uses to look up the current version's physical address
  * Secondary index reads may be slower as the DBMS has to resolve the logical identifier

## Problem #4: Vacuum Management
* By default, autovacuum does not run until 20% of a specific table's tuples are dead tuples
  * So for large tables, this can mean that dead tuples are kept alive for a long time
