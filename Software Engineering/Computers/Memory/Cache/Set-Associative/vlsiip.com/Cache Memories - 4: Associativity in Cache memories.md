# [Cache Memories - 4: Associativity in Cache memories](http://vlsiip.com/cache/cache_0003.html)

## 2-way set associative cache example
* Each cache set contains a set of 2 cache lines
* Cache memory address = 16 bits
* Total cache size = 0.5 KB
* Cache line = 32 bytes / 256 bits
* Offset address within a cache line = `log2(cache line size in bytes)` = 5 bits
* Total number of cache lines = total cache size / cache line size = 16 cache lines
* Number of sets = Number of cache lines / set associativity = 16 / 2 = 8
* Set address width (in bits) = `log2(number of sets)` = 3 bits
* When a new cache address is issued, the cache controller uses 3 bits of the new cache address to identify one of the 8 sets, each comprised of 2 "tags"
  * The cache controller will then compare the 8 bits of the cache address with each of the 2 "tags" in the identified sets
  * If there is a match, there will be a single output bit (`0`, or `1`) to indicate which tag in the set matched
  * The offset portion of the address indicates where to start reading in the cache line that maps to the matched set "tag"
