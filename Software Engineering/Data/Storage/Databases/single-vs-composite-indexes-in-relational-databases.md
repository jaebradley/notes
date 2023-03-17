# [Single vs. Composite Indexes in Relational Databases](https://medium.com/@User3141592/single-vs-composite-indexes-in-relational-databases-58d0eb045cbe)

* In relational databases, an index is a data structure that increases retrieval speed at the expense of decreasing write speed as well as using more storage space
* Unless a column value is unique, looking up a column by a value will require a full table scan
  * Even if a column is unique, looking up a column by a value will require scanning `n/2` rows, on average
* By storing values in an index (typically via `b-tree`), can cut down on query time `O(log(n))` since we're effectively binary searching on a field
* Don't use an index
  * for low-read / high-write tables
  * do not use indices for low cardinality / distinct values in field
    * whole point of binary search is roughly eliminating half of the records with each step
  * for small fixed-size tables
* Composite indices is an index for the concatenation of column values
  * If a composite index is made up of `ColumnA` and `ColumnB`, then a query like `WHERE ColumnA = 'A' AND ColumnB = 'B'` will have improved performance
  * And a query like `WHERE ColumnA = 'A'` will also see improved performance
  * But a query like `WHERE ColumnB = 'B'` will not see improved performance
  * *Ordering matters*
