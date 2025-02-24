# [Scaling Memcache at Facebook](https://scontent-bos5-1.xx.fbcdn.net/v/t39.8562-6/240873052_277412237132971_6278324660880331641_n.pdf?_nc_cat=101&ccb=1-7&_nc_sid=e280be&_nc_ohc=3hLdwWj30iMAX8RNt8X&_nc_ht=scontent-bos5-1.xx&oh=00_AfCmhjnilYEkX0OC6Zy4Tc7Mvr4euBoyXFHBML-VuvlBRw&oe=658A7382)

## Overview

### Query cache

* Demand-filled look-aside cache
  * Web server needs data
  * Requests data for a string key from distributed `memcache`
  * If the key is not cached, web server retrieves the data from the database and populates the cache with the key/value pair
  * For write requests, web server executes SQL statements to database
    * Delete request to distributed `memcache` as deletes are idempotent
    * Idea is that distributed `memcache` is not the authoritative source of data and allowed to evict cached data

### Genric cache

* Engineers use distributed `memcache` to store pre-computed results to be used by other applications
* The `memcached` binary is an in-memory hash table running on a single server
