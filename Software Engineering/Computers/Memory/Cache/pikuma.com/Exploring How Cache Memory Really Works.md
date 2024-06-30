# [Exploring How Cache Memory Really Works](https://pikuma.com/blog/understanding-computer-cache)

* Usually think about RAM as a long array of bits but really it’s more accurate to think about RAM using a table
* Blocks of bytes (“a cache line”)
* Cache can load N blocks inside
* Different processor microarchitectures can specify different sizes for their cache lines
* Cache line size is the size of the unit of data transferred to and from main memory
* Larger cache line sizes means exploiting the spatial locality of data more effectively
* L1 ~ 3 CPU cycles
* L2 ~ 10 cycles
* RAM ~ 250 cycles

# Where to put data in cache?

* Direct-mapped cache - each memory address maps to one cache line / block
* In example, memory location 0, 4, 8, 12 map to cache block 0
* Memory location 1, 5, 9, 13 map to cache block 1
* Memory locations 0-15 inclusive each map to a specific cache block
* Can compute cache block using modulo
* So multiple addresses could map to the same cache block - how to distinguish between these potential addresses that all map to the same cache block
* Tags supply the rest of the address bits and distinguish between different memory locations that map to the same cache block
* When data is loaded into a particular cache block, the corresponding valid bit flag is set to 1
