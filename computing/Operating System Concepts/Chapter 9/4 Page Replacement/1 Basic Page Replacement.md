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
 * If the modify bit is not set, then we know the page

  
