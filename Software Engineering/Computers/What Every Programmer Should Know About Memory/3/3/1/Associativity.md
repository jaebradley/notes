# Associativity

* For L1i, L1d, and higher level caches, directly-mapped caches are used
* Directly-mapped caches use bits 6 to 21 (16 bits total) to map a tag to each of the ~65k entries  (4MB cache with 64B cache lines)
  * The low 6 bits are the index into the cache line
* Directly-mapped caches work well if addresses used by programs are evenly distributed with respect to the bits used for direct mapping
  * If they are not, some cache entries are heavily used and are repeatedly evicted while others are hardly used at all or remain empty (this is usually the case)
* Set Associative caches divide the tags into sets
  * Given the address of a cache line, one of these sets is selected
  * Instead of having exactly one element for each set value, a small number of values are cached for the same set value
  * The tags of all the set members are compared in parallel
  * Size of the cache is not limited by number of comparators - comparators would only increase if the associativity level increased
  * Most L2 caches have associativity levels up to 24 while L1 caches get by with 8 sets
* Cache size = cache line size x associativity x number of sets where associativity is the number of elements in each set
