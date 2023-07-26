# 13.4.3 Caching

* Cache is a region of fast memory that holds copies of data
  * Accessing cached copy is more efficient than access to the original data
* Difference between buffer and a cache is that buffer may be only copy of data
  * By definition, cache holds a copy on faster storage of item that resides elsewhere
* Buffers may be used as caches
  * OS uses buffers in main memory to hold disk data
  * When kernel receives a file I/O request, kernel first checks the buffer cache to see if the region of the file is already available in main memory
    * If so, can avoid a physical disk I/O operation
