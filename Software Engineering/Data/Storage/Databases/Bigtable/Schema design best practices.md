# [Schema design best practices](https://cloud.google.com/bigtable/docs/schema-design)

* Schema design is usually driven by optimizing for row range reads (i.e. querying based on row key prefix)
* To prevent hotspotting, avoid write patterns that access a small key space in a short amount of time

## General concepts

* Bigtable is a key/value store
* Each table has a single index, the row key
* Rows are sorted lexicographically, based on the row key
* Column families are not stored in any particular order
* Colums are grouped by column families and sorted in lexicographic order within a column family
  * Column qualifiers of `ProcessName`, `User`, `%CPU`, `ID`, `Memory`, `DiskRead`, `Priority` would be ordered
  * `%CPU | DiskRead | ID | Memory | Priority | ProcessName | User`
* Intersection of row and column can contain multiple timestamped cells
* Operations are atomic at the row level
* Columns do not take up space in a row that doesn't use the column
