# [Redis vs. Memcached: In-Memory Data Storage Systems](https://alibaba-cloud.medium.com/redis-vs-memcached-in-memory-data-storage-systems-3395279b0941)

# Different Memory Management Schemes

* When the physical memory is full, Redis may swap values not used for a long time to the disk
  * Redis only caches all the key information
  * If it discovers taht the memory usage exceeds the threshold value, it will trigger the swap operation
  * Redis calculates the values for the keys to be swapped to the disk
  * It then makes these values for the keys persistent into the disk and erases them from memory
  * This feature enables Redis to maintain data of a size bigger than its machine memory capacity
