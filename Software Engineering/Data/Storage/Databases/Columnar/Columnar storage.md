# [Columnar storage](https://docs.aws.amazon.com/redshift/latest/dg/c_columnar_storage_disk_mem_mgmnt.html)

* Row-based databases store all row values for a single record, sequentially
  * This can cause an entire row to be stored in more than one block if the record size is greater than the block size
* Columnar-storage stores values for a single column, across many rows
  * Tables with very large number of columns and a large number of rows see storage efficiency gains
  * Since each column has the same type, and thus each storage data block has the same type, specific compression encodings can be used for a specific column data type
  * Many database operations only need to access or operate on a small number of columns at any time, so you can only retrieve the blocks for a given column that you care about
  * Row-based databases would need to read all column values, leading to many wasted I/O operations
