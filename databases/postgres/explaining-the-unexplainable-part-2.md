# [Explaining the Unexplainable - Part 2](https://www.depesz.com/2013/04/27/explaining-the-unexplainable-part-2/)

* Sequence Scan
  * Postgres opens table file and reads rows one by one
  * Returns results to user or upper node in explain tree (upper node like limit)
  * Order of rows is not order of insertion or last updated first - vacuums, inserts, deletes can all impact order of rows returned
* Index Scan
  * Looks at index
  * Find where in table data that rows might exist that match condition
  * Open table
  * Fetch rows specified by index
  * Return rows if they are visible to current session
    * For example, rows that are inserted _after_ current transaction should not be returned
  * Index scan will also be used for `ORDER BY`
* Bitmap Scan
  * Uses at least two nodes
    * First node uses Bitmap Index Scan
    * Second node uses Bitmap Heap Scan
  * Index scan keeps a bit for every single page and if page might include row, then the bit will be flipped from `0` to `1`
    * So a table with one hundred thousand pages (~780MB) would generate a block of 100,000 bits or 12.5kb
  * The point of bitmap scan is that normal index scans causes random IO, i.e. pages from disk are loaded in a random fashion, which can be slow
    * So a bitmap scan is helpful in cases where you want many rows of the table, but not all, and you're not loading a block of table data
  * Bitmap index scans can be joined which means that logical operations can be performed on two bitmaps (Or / And / Not)
