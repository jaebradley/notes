# Bypassing the Cache

* The fact that memory store operations read a full cache line first and then modify the cached data is detrimenal to performance
  * This operation pushes data out of the caches which might be needed again, in favor of data which will not be used soon
  * Take matrices - before the last element of the matrix is filled, the sheer size evicts the first elements, making caching of the writes ineffective
* Processors provide non-temporal write operations, which means that data will not be reused soon, so there is no reason to cache it
  * Non-temporal write operations do not read a cache line and then modify it - the new content is directly written to memory
* 2D array representing matrix where first index represents the row and the second index addresses the elements in the row
  * Test program iterates over the matrix by increasing the column number in the inner loop, and then by increasing the row index in the inner loop (so iterating over the columnes in each row, and then iterating over the rows in each column)
  * If memory is used sequentially the creation of a 3000 x 3000 matrix takes 0.048s (~750MB/s) compared to the column incrementing, which represents more-or-less random access, which takes 0.127 seconds (~280MB/s)
  * For writes that bypass the cache (using the non-temporal hint) sequential memory access (i.e. row iteration) is just as fast as when the cache is used due to write-combining (i.e. writing many words that are part of the same cache line to the cache in a single cache update)
  * For writes that bypass the cache for non-sequential memory access (i.e. column iteration) it takes 0.16 seconds to create the matrix (~225MB/s). No write combining is possible since the values are non-sequential, so this requires constantly selecting new rows in the RAM chips
