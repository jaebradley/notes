# [UUIDs are Popular, but Bad for Performance — Let’s Discuss](https://www.percona.com/blog/uuids-are-popular-but-bad-for-performance-lets-discuss/)

## Row Insertion Behavior

* Row is inserted with a new UUID primary key value
* InnoDB finds page where row should be stored by traversing b-tree
* Loads page from disk into a buffer
* Inserts row to page
* Flushes page from memory back to disk

### Impact on I/O

* With purely random values, all b-tree leaf pages are equally susceptible to receive the new row
* With UUID primary keys, there is no natural order, so pages are scattered, with a low fill rate
  * In other words, there are many pages, with small amounts of data
* Since table pages are read from disk on row insertion with a UUID primary key, it is unlikely that the next row insertion will use the same table page
  * So current table page in buffer will need to be flushed to disk, and the next table page needs to be read from disk into the buffer
  * Every insert will result in a read and a write I/O operation (read next table page, write current table page)

### Impact on Indices

* Secondary indices use the primary key values as pointers
  * Leaves of the b-tree of a secondary index store primary key values
* Table with 1B rows, UUID PK, 5 secondary indices
  * Primary key values are stored 6 times for each row
  * 6B x 128 bits -> 96 GB
  * Analysis of a UUID-based schema found that 70% of storage was for just the UUIDs

### Value comparison

* Integer values are compared up to 8 bytes at a time
* UUID values are compared char by char
* Databases are rarely CPU-bound, but char vs. integer adds latencies to queries
  * Comparing integers vs. uuids is 2.5-28x faster (depending on where a character difference first occurs)

## Reduce I/O Operations Using Pseudo-Random Order

* The random nature of UUIDs causes the greatest performance impact
* Use the same small byte prefix over a specific time interval
* The set of table pages that could be inserted into now corresponds to the cardinality of the prefix
  * To save on read I/O, these table pages would be required to be in memory
  * Shared prefix increases likelihood that the current page would also receive a second write before being flushed to disk
