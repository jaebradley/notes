# 9.4.1 Basic Page Replacement

## Introduction

* If a process of ten pages actually uses only half of the pages, on-demand paging saves the I/O necessary to laod five pages that are never used
  * If there were forty frames of physical memory, could run eight processes (`5` frames per process) vs. only four processes without on-demand paging / if all ten pages were required
  * If each of these processes suddenly needed to use all ten pages, there is a need for eighty frames, and memory has now been over-allocated
* When a user process is executing, a page fault occurs
* Operating system identifies where the desired page is on disk
* Operating system tries to find a free frame but no free frames exist - all memory is being used
* Operating system swaps out a process, freeing all the process's frames

## Implementation

* Find the location of the desired page on disk
* Find a free frame - if one doesn't exist, use the page-replacement algorithm to select a victim frame
 * If no free frames, there are two page transfers for transferring the existing page to disk, and transferring the desired page from disk to main memory
 * This effectively doubles the page-fault service time / effective access time
* Write the "victim" frame to disk, change the page and frame tables
* Read the desired page into the newly freed frame, change the page and frame tables
* Restart the user process

### Modify bit

* Each page / frame has a modify bit associated with it
* When the page is written to, the modify bit is set to indicate that the page has been modified
* When a page is about to be replaced, if the modify bit is set, we know the page has been modified since it was read from disk, so we must write the page to disk
 * If the modify bit is not set, then we know the page has not been modified since it was read from disk, thus no need to write the page to disk
 * Since these unmodified pages can be discarded without writing to disk, can reduce the I/O time to service a page fault by half (since the target page still needs to be read from disk)

## Implications

* Without demand paging, all the pages of a process must reside in physical memory
* With demand paging, the size of the logical address space is no longer constrained by physical memory
* If we have a user process that requires 20 pages, can use 10 physical memory frames using demand paging, and use the replacement algorithm to find a free frame
  
