# Cache Operation at High Level

* By default, all data read or written by the CPU cores is stored in the cache
* If the CPU needs a data word, the caches are searched first
  * Each cache entry is tagged using the address of the data word in main memory (virtual or physical address)
* Since neighboring memory is likely to be used together, it should also be loaded into cache together
  * RAM moduels are more effective if they can transport many data words in a row without a new CAS / RAS signal
* When memory content is needed by the processor, the entire cache line is loaded into the L1 data cache
* The tag is also part of the memory address for each cache line
* Whena an instruciton modifies memory, the processor still has to load a cache line first because no instruction modifies an entire cache line at once
  * The content of the cache line before the write operation therefore has to be loaded
  * It is not possible for a cache to hold partial cache lines
  * A cache line which has been written to and which has not been written back to main memory is said to be "dirty"
  * Once it is written, the dirty flag is cleared
* To be able to load new data in a cache, it is almost always first necessary to make room in the cache
  * An eviction from L1d pushes the cache line down into L2
  * This means that room has to be made in L2, which might push content into L3 and ultimately into main memory
  * Each eviction is progressively more expensive
* In symmetric multi-processor systems, the caches of the CPUs cannot work independently from each other
  * All processors are supposed to see the same memory content at all times
  * Processors detect when another processor wants to read or write to a certain cache line
  * If a write access is detected and the processor has a clean copy of the cache line in its cache, this cache line is marked invalid
* If a cache line is dirty in one processor's cache and a second processor wants to read or write that cache line, main memory is out of date
  * The requesting processor must get the cache line content from the first processor, bypassing main memory
* A dirty cache line is not present in any other processor's cache
* Clean copies of the same cache line can reside in arbitrarily many caches
