# [Open addressing](https://en.wikipedia.org/wiki/Open_addressing)

* Hash collisions are resolved by probing, or searching throoough alternative locations in the backing array, until the target record is found, or an unused array slot is found
* Different probing strategies include 
  * linear probing (fixed interval probing - i.e. check the following array slot, check the array slot after that, etc)
    * Best cache performance, but is sensitive to clustering
  * quadratic probing (interval between probes increases quadratically - i.e. check next slot, check two slots after, check four slots after, check 16 slots after)
  * double hashing (some other hash function is used to determine the next interval)
    * Poor cache performance, no clustering, more computation
* The proportion of the array slots that are used (load factor) influences performance
  *  As the load factor increases towards 100%, the number of probes that are required to find or insert a given key increases (finding a free slot in the array takes longer and longer)
  *  Even with good hash functions, load factors are normally limited to 80%, usually 50%
* The algorithm for finding an open array slot for a given key using open addressing (this example uses single-slot stepping, linear probing - i.e. check the next index in the backing array)
  * Hash the key and modulo the number of slots in the array - this returns the index that the value would be at assuming no collision
  * If there is a collision, increment the index value by 1 (making sure to wrap around from the end of the array to the beginning)
  * Continue this process if there exists a value at the index and the value at the index is _not_ the key
  * The process stops if an empty slot is found _or_ if a non-empty slot is found with a key that matches the input key 
