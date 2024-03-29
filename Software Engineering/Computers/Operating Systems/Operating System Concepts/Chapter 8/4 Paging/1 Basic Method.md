# 8.4.1 Basic Method

* Break physical memory into fixed-sized blocks called frames
* Break logical memory into blocks of the same size as frames (logical memory blocks are called "pages")
* When a process is loaded into memory, its logical memory pages are loaded into any available physical memory frames
* Every logical address generated by the CPU has a page number and a page offset id
  * The page number is an index value into a page table
  * Every process has its own page table
  * This page table contains the base address of each logical memory page in physical memory (i.e. the beginning of the corresponding physical memory frame)
 * The base address is combined with the page offset id to define the physical memory address that is then used by the memory unit
* Generally, page sizes are powers of 2
 * If the size of the logical address space (i.e. the page offset id value) is `2^M` and a page contains `2^N` addresses then the high-order `M-N` bits define the page number
* Addresses generated by the CPU look like `|page number (m -n bits)| page offset (n bits)|` 

```
Logical Memory

_______
Page 0 |
_______|
Page 1 |
_______|
Page 2 |
_______|

Page Table

* First column is the logical memory page number
* Second column is the physical memory frame number

______
0 | 1 |
__|___|
1 | 4 |
__|___|
2 | 3 |
__|___|

Physical Memory

* Page offset is used here to find physical memory address within the corresponding frame

_________________
Frame 0 - No Page|
_________________|
Frame 1 - Page 0 |
_________________|
Frame 2 - No Page|
_________________|
Frame 3 - Page 2 |
_________________|
Frame 4 - Page 1 |
_________________|
Frame 5 - No Page|
_________________|
```

* Paging itself is a form of dynamic relocation
  * Every logical page of memory basically has its own base / relocation register that corresponds to a frame of physical memory
* There should be no external fragmentation as any free frame can be allocated to a process
* There may be internal fragmentation
  * Will occur if the process's memory requirements do not align with page boundaries, then the last frame will not be completely full
* Typically, pages are between 4-8 KB
* User program views memory as one single contiguous space
  * But the actual physical memory is scattered
* The user process cannot access / address memory it doesn't own
  * It cannot address memory outside of its page table
  * Page table only includes the pages that the process owns
* Operating system must be aware of the number of frames allocated, frames available, etc
* Frame table has one entry for each physical page frame
  * Indicates if frame is free, or which page it is allocated to
