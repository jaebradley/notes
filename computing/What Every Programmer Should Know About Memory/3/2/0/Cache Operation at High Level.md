# Cache Operation at High Level

* By default, all data read or written by the CPU cores is stored in the cache
* If the CPU needs a data word, the caches are searched first
  * Each cache entry is tagged using the address of the data word in main memory (virtual or physical address)
* Since neighboring memory is likely to be used together, it should also be loaded into cache together
  * RAM moduels are more effective if they can transport many data words in a row without a new CAS / RAS signal
* When memory content is needed by the processor, the entire cache line is loaded into the L1 data cache
* The tag is also part of the memory address for each cache line
