# [How does SQLite work? Part 2: btrees! (or: disk seeks are slow don't do them!)](https://jvns.ca/blog/2014/10/02/how-does-sqlite-work-part-2-btrees/)

* Reading more data than you absolutely need to read is really expensive because seeking to a new location in a file takes a long times
  * It takes much less CPU time to seach through your data than it does to read the data into memory
* Filesystems have a block size of X (like 4kb) which means that it's impossible to read less than X at a time

## The index and table btrees

* Each table has a btree, made up of interior and leaf nodes
  * Leaf nodes contain the dadta and interior nodes point to other child nodes
* Every index in the table has its own btree, where you can look up which row id a column value corresponds to
  * Maintaining lots of indices is slow because every time a row is inserted, each index btree needs to be updated
* When doing a query like `select * from x where y = z`, there are actually two different binary searches in two different btrees
* The first binary search looks for the row id value associated with `z` in the index btree
* The second binary search looks for the row in the table btree associated with the row id from the first search
