# 9.8.1 Buddy System

* Allocates physically contiguous pages of memory
* Memory is allocated in units that are powers of 2 (4KB, 8KB, 16KB, etc)
  * Requests that are not a power of 2 are rounded up to the next highest power of 2 (11KB -> 16KB)

## Example

* Memory segment of 256KB
* Kernel requests 21KB
* 256KB divided into two 128KB "buddies"
* One 128KB "buddy" is further divided into two 64KB "buddies"
* One 64KB "buddy" is divided into two 32KB "buddies"
* One of the 32KB "buddies" is used to satisfy the kernel's request for 21KB
* Advantage of buddy system is that adjacent buddies can be coalesced into larger segments
  * When kernel releases the 21KB memory segment and the 32KB memory segment is available again, the 32KB segment and its buddy can easily be coalesced into a 64KB segment
* Disadvantage as fragmentation is very likely to occur
  * A 33KB request can only be satisfied with a 64KB segment
