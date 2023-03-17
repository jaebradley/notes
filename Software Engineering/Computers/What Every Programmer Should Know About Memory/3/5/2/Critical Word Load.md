# Critical Word Load

* Memory is loaded from main memory into caches in blocks that are smaller than the cache line size
  * 64 bits are transferred at once and the cache line size is 64/128 bytes so 8 or 16 transfers per cache line are needed
* On a cache miss, the required word inside the cache line for a program to continue may not be the first word in the cache line
  * Each 64-bit block arrives 4 CPU cycles or more after the previous other
  * So if the required word for the program is the eighth word in the cache line, the program has to wait 30 more cycles
* The memory controller is free to request the words of the cache line in a different order
  * The processor can communicate which word the program is waiting on, the critical word, and the memory controller can request this word first
  * Once the word arrives, the program can continue while the rest of the cache line arrives and the cache is not yet in a consistent state
* If a processor prefetches data, the critical word is not known
  * Should the processor request the cache line during the time the prefetch operation is in flight, it will have to wait until the critical word arrives without being able to influence the order

