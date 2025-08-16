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

## Best Practices

### Tables
* Better to store all data in one table (assuming data has similar schemas)
* Can assign a unique row key prefix to use for each dataset
 * Bigtable stores the related data in a contiguous range of rows that can be queried using the prefix
* Bigtable has a limit of 1,000 tables per instance
* Many different tables can lead to icnreased backend connection overhead
* More tables doesn't improve load balancing

### Column families
* Put related columns in the same columnn family
* Row containing multiple values that are related to one another should be grouped in a column family
* Create up to 100 column families per table
* Column families should have short names as they are transferred for each request
* Columns that have different data retention needs should be in different column families
 * Garbage collection policies are set at the column family level and not the column level

### Columns
* No space penalty for a column that is not used in a row
* No row should exceed maximum limit of 256 MB per row
* Each cell adds overhead to the stored data
 * More efficient to store 1KB of data in one cell, than 1 byte across 1,024 cells
* Can treat column qualifiers as data
 * Example is a friendship mapping where the row key is a person ID and the columns are person IDs
 * Cell values in this mapping are the social group that owns the friendship connect (like a book club)

<img width="1520" height="300" alt="Screenshot 2025-08-16 at 16 31 27@2x" src="https://github.com/user-attachments/assets/85912b6f-1b97-4ca4-98f1-b9713221bc22" />

### Rows
* Avoid storing data that must be read atomically across more than one row
* Ensure that schema does not require more than one row to be updated at the same time for related data to be accurate
* Store related entitites in adjacent rows to make reads more efficient

### Cells
* Don't store more than 10 MB of data in a single cell
* Use aggregate cells to store and update aggregate data

### Row Keys
* Efficient queries retrieve data using the row key, the row key prefix, or a range of rows defined by a starting and ending row key
* Row key must be less than 4 KB
* If your row key includes integer that you want to store or sort numerically pad the integers with leading zeroes as Bigtable stores data lexicographically
* Avoid row keys that start with a timestamp
 * Can push sequential writes into a single node, creating a hotspot
* Similarly, avoid row keys with sequential numeric IDs (like user IDs)
 * New users are more likely to be more active users
 * Traffic may be pushed into a small number of nodes
 * Solution might be a reversed version of the uerer's numeric ID, which would spread traffic evenly across nodes
* Avoid row keys with hashed values as it removes Bigtable's natural sorting order
* Can support multi-tenancy in one table via the row key prefix
