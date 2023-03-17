# [How to scale PostgreSQL 10 using table inheritance and declarative partitioning](https://medium.com/timescale/scaling-partitioning-data-postgresql-10-explained-cd48a712a9a1)

* Relational databases store tables as pages of data (8kb in PostgreSQL)
  * B-trees are built to index the data for faster lookups
  * If data is large enough that can't fit all pages of index into memory, then updating random part of tree can involve significant disk I/O as pages are read from disk into memory, modified in memory, and then written back to disk
  * Problem is compounded as index more columns

## Partioning

* Avoids expensive swap-to-disk problems
* Split large table into smaller sub-tables
* By being able to fit indices into memory, inserts and queries are improved
* Smaller datasets per partition enable database to use sequential scans efficiently
* Maintenance operations like `VACUUM` and `REINDEX` run faster because of smaller indexes
* Deleting entire partitions of data is faster since can `DROP TABLE` now instead of expensive vacuuming operations

### Table Inheritance

* Before PG 10, table partitioning was only possible via table inheritance
* Parent table is created and then each partition is represented by a child table which `INHERITS` from parent table using `INHERITS` clause
* When querying parent table, data from both parent and child tables is returned
* Add constraints to child tables to define the specific row value ranges in each partition
* Create indices on parent and child tables individually (indices do not propagate from parent tables to child tables)
* Write a trigger function so that inserts into parent table insert into appropriate partition table
* Redefine trigger function when child tables change

#### Table Inheritance Limitations

* Data consistency depends on `CHECK` constraints defined on child tables
* `INSERT` and `COPY` commands do not propagate data to child tables but must rely on triggers which results in slower inserts
* Re-partitioning is non-trivial (when making schema changes, for example)
* Ensuring uniqueness across partitioned tables is not supported
* Table maintenance (like adding indices) need to be applied to child tables explicitly

### Declarative Partitioning in PG 10

* Partition by a `RANGE` or a `LIST`
* `RANGE` - partition table into "ranges" defined by a column or set of columns where there is no overlap between the ranges between different partitions (like a UUID)
* `LIST` - partition table by specifying key values
  * Limited to a single column
  * Example could be `device_type`
* Created by using a `PARTITION BY` clause like `PARTITION BY RANGE (logdate)`
* After creating partitioned table, specific partitions can be manually created
  * `CREATE TABLE measurement_y2006m02 PARTITION OF measurement FOR VALUES FROM ('2006-02-01') TO ('2006-03-01')`
  * `CREATE INDEX ON measurement_y2006m02 (logdate);`
* Users can insert data into underlying child tables via partitions since tuples / rows are automatically routed to right partition on `INSERT` and don't need to depend on triggers (which was the case with table inheritance)

#### Declarative Partitioning Limitations

* Child tables for data need to exist before data is inserted
* If partitioning by time, would be nice if system auto-created new partitions as new data came in
* Cannot create indices on all partitions automatically - indices need to be created manually for each partition
* Updates that would move row from one partition to another will fail
* Row triggers must be defined on individual partitions
* Cannot create primary keys on partitions so foreign keys referencing partitioned tables are not supported nor are foreign keys from a partitioned table to another table
* No support for enforcing uniqueness across an entire partitioned table - only on individual partitions
