# 12.6.3 Swap-Space Management: An Example

* Initially, the UNIX kernel implementation swapped entire processes between contiguous disk regions and memory
* In Solaris 1, processes would read pages of code from the file-system
  * Access these pages in main memory
  * Throw away pages if selected for pageout
  * More efficient to reread a page from the file system than to write it to swap space and then reread it from swap space
  * Swap space only used as a backing store for pages of "anonymous" memory
    * Includes memory for the stack, heap, and uninitialized data of a process
* Later versions of Solaris only allocated swap space when a page was forced out of physical memory rather than when the virtual memory page is first created
  * Better performance on computers with more phsyical memory and page elss (modern computers)
* Linux uses swap space for "anonymous" memory or for regions of memory shared by several processes
* Linux allows multiple swap areas (file system or a raw swap-space partition)
  * Swap areas consist of 4-KB page slots, which hold swapped pages
  * A swap map keeps a count for each page slot
    * When the count is greater than 1, this indicates that the swapped page is mapped to three different processes
      * Swapped page is storing a region of memory that is shared by three processes
