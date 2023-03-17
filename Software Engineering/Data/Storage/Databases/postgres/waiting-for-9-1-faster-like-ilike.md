# [Waiting for 9.1 - Faster LIKE/ILIKE](https://www.depesz.com/2011/02/19/waiting-for-9-1-faster-likeilike/)

* `LIKE` operations without indices are slow
* Using `pg_trgm` extension and creating a `gin` index on a text column can increase search by two orders of magnitude
  * `ILIKE` is only 4x slower
* Creating index takes much longer than regular btree index
  * btree index took 8k ms while GIN index took 2.9 million ms (50 minutes) to add 2 million rows
    * This was index creation not adding to existing indices
* `GIST` is faster to index but slower on search
