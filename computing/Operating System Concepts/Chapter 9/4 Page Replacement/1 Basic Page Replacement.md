# 9.4.1 Basic Page Replacement

## Introduction

* If a process of ten pages actually uses only half of the pages, on-demand paging saves the I/O necessary to laod five pages that are never used
  * If there were forty frames of physical memory, could run eight processes (`5` frames per process) vs. only four processes without on-demand paging / if all ten pages were required
  * If each of these processes suddenly needed to use all ten pages, there is a need for eighty frames, and memory has now been over-allocated
* When a user process is executing, a page fault occurs
* Operating system identifies where the desired page is on disk
* Operating system tries to find a free frame but no free frames exist - all memory is being used
* Operating system swaps out a process, freeing all the process's frames


  
