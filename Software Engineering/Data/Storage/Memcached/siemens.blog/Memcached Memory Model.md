# [Memcached Memory Model](https://siemens.blog/posts/memcached-memory-model/)
* Memcached creates slabs that are associated with certain byte size ranges

```
slab class   1: chunk size        96
slab class   2: chunk size       120
slab class   3: chunk size       152
slab class   4: chunk size       192
slab class   5: chunk size       240
```

* If an item with a size of `80` bytes is `set` then it will be stored on a page associated with slab 1
  * This page will be divided into `96` byte chunks
  * In the case of the `80` byte item, there will be `16` bytes of wasted memory
* When an item is associated with a particular slab class, memcached requests a page of memory for that slab
* A page is a fixed size of 1 MB of memory by default
* Once a page is assigned to a specific slab class, it is never moved
  * This means that if a bunch of items in slab class `X` expire it *might* look like there are a bunch of free pages worth of memory
  * But if all items that are being stored are in slab class `Y` , and pages don't change change slab class, the class `X` pages are not available for the class `Y` items
  * So if there is no more memory allocatable for class `Y` items, then the oldest class `Y` items will start being evicted to make room for the new items
* This eviction churn results in items that are `set` to expire in many hours disappearing from the cache in a couple seconds
* This can continue until access patterns change or memcached is restarted
* As of memcached 1.4.25, there is a feature called slab automover that will move free pages into a global pool and reassign them to a new slab class
