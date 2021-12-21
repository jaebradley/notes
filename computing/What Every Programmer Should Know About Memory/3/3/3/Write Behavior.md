# Write Behavior

* If a cache line is modified, the result for the system after this point in time is the same as if there was no cache at all and the main memory location itself had been modified
* Write-through cache is when if the cache line is written to, the processor immediately also writes the cache line into main memory
  * Ensures that main memory and the cache are in sync at all times
  * Simple, but not very fast
  * A program that modifies a local variable over and over again would create a lot of traffic on the FSB even though the data is likely not used anywhere else and might be short-lived
* For a write-back cache, the processor does not immediately write the modified cache line back to main memory
  * Instead, the cache line is marked as dirty
  * When the cache line is dropped from the cache at some point in the future, if the cache line is dirty, the processor will write the data back into main memory at that time
  * When more than one processor or core or hyper-thread is available and accessing the same memory, it must still be assured that both processors see the same memory content at all times
  * If a cache line is dirty on one processor (i.e. it has not been written back yet) and a second processor tries to read the same memory location, the read operation cannot just go out to the main memory
    * The content of the first processor's cache line is needed 
* Write-combining is a caching optimization
  * Transferring an entire cache line just because a word in the line has been written is wasteful if the next operation modifies the next word
  * Write-combining combines multiple write accesses before the cache line is written out
  * In ideal cases, the entire cache line is modified word by word, and only after the last word is written is the cache line written to the device
