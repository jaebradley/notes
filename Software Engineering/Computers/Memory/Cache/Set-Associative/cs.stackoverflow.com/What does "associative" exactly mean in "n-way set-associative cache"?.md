# [What does "associative" exactly mean in "n-way set-associative cache"?](https://cs.stackexchange.com/a/139709)
* An n-way set associative cache is a cache that is chopped up in sections called sets
* Each set can hold n-blocks
* Cache address is broken up into 3 parts
  * Offset within the block
  * Index that identifies the set
  * Tag that identifies the block in the set
* Cache receives a data request, the specific set index is calculated
* Tags of blocks in set are checked
* When a block with a matching tag is found, the bytes are returned based on the offset
* Direct-mapped cache is a 1-way set associative cache
