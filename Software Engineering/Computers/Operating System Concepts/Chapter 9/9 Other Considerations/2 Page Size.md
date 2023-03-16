# 9.9.2 Page Size

## Page Table Size

* Decreasing the page size increases the number of pages and the size of the page table
* Each active process must have its own copy of the page table

## Internal Fragmentation

* Processes will acquire memory, and they will not end on page boundaries, which will lead to internal fragmentation
* On average, half of the final page of each process will be wasted
* Loss is only 256 bytes for a page of 512 bytes, but is 4,096 bytes for a page of 8,192 bytes
* Smaller page sizes minimize internal fragmentation

## I/O Times

* I/O times are composed of seek, latency, and transfer times
  * Transfer time is proportional to the amount transferred (i.e. page size)
  * Latency and seek times are much greater than transfer time
  * 512 bytes would take 0.2 milliseconds with a transfer rate of 2MB/second
  * Latency would be 8 milliseconds and seek time would be 20 milliseconds
  * So 1% of total cost is the transfer time
  * A single page of 1,024 bytes would take 28.4 milliseconds, but reading two pages of 512 bytes would take 56.4 milliseconds
* Smaller page sizes will reduce total I/O as there will be greater locality
  * A process that is 200kb in size, but only 100kb is used
  * If there is a single page of 200kb, then a total of 200kb is transferred and allocated
  * If pages were instead 1 byte in size, only the 100kb would actually be transferred and allocated
  * Potentially up to 102,400 page faults with 1 byte pages - potentially a lot of page faults, which have a large amount of overhead
