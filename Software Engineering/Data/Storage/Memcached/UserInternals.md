# [UserInternals](https://github.com/memcached/memcached/wiki/UserInternals)
* Memory is assigned with the `-m` command line argument
* Primary storage is broken up by default into 1 MB pages
* Each of these pages is assigned into slab classes, and then cut into chunks of a specific size for that specific slab class
* Once a page is assigned to a class, it is not moved
* Mental model is that memcached is actually many smaller individual caches where each class has its own set of counters and its own LRU
* Classes and chunks are reported by executing the `./memcached` program with the `-vv` flag

```
$ ./memcached -vv
slab class   1: chunk size        80 perslab   13107
slab class   2: chunk size       104 perslab   10082
slab class   3: chunk size       136 perslab    7710
slab class   4: chunk size       176 perslab    5957
slab class   5: chunk size       224 perslab    4681
slab class   6: chunk size       280 perslab    3744
slab class   7: chunk size       352 perslab    2978
slab class   8: chunk size       440 perslab    2383
slab class   9: chunk size       552 perslab    1899
slab class  10: chunk size       696 perslab    1506
[...etc...]
```

* In the above example, slab class 1 has chunks of 80 bytes and thus, each page can contain \~13k items
* Item storage will push data into whatever slab class has the nearest fit
  * So if the key + internal data structure for the item + value = 50 bytes, this data will go into class 1 with an overhead loss of 30 bytes
  * If this data is 90 bytes total, this data will go into class 2, with an overhead of 14 bytes
* Other allocated memory: hash table used to look up items, each connection uses small buffers
* Memory is reclaimed when fetching an expired item
  * Memcached will find the item, notice it is expired, and free memory
* Items are evicted if they not expired, the slab class is completely out of free chunks, and there are no free pages to assign to a slab class
* Memory is reclaimed when it is time to store a new item
  * If there are no free chunks and no free pages in the appropriate slab class, memcached will look at the end of the LRU for an item to reclaim
  * Memcached will search the last few items in the tail of the LRU for an item that has already been expired, and is free from reuse
  * If no expired item is identified, it will evict an item that is not expired
* Memcached uses `libevent` to help handle tens of thousands of server connections
  * Each worker thread on memcached runs its own event loop and handles its own clients
