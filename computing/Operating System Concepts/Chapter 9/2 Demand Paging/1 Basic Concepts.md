# 9.2.1 Basic Concepts

* Naive option is to load the entire executable program from disk into physical memory at program execution time
  * May not need the entire program
  * Load pages only as they are needed
* When a process is going to be swapped, the pager guesses which pages will be used before the process is swapped out again
* Use the valid/invalid bit scheme in the page table to distinguish between pages that are in memory and pages that are on disk
  * An "invalid" bit means that the page is not valid (i.e. in the logical address space of the process) or is valid, but is currently on disk and not in physical memory
* Marking a page invalid has no effect on the program if the page is never needed / process never attempts to access that page
  * In other words, if the pager guesses correctly, and identifies the exact pages the program will actually access, the process will run as though all pages were brought into memory
  
## Accessing an invalid page

* Accessing an invalid page causes a page fault
* Causes a trap to the operating system
* Check an internal table (usually in the Process Control Block) to see if the address is valid for the process
  * If the address is not in the logical address space of the process, the process is terminated
* If the address is valid, find a free physical frame
* Schedule a disk operation to read the desired page into the newly allocated frame
* When the disk read is complete, modify the internal table kept with the process and the page table to indicate that the page is now in memory
* Restart instruction that was interrupted by the trap
* Process can act like the page has always been in memory

## Restarting any instruction after a page fault

* The state of the process (registers, instruction counter etc) is saved when the page fault occurs
* If the page fault occurs on instruction fetch, can restart by fetching the instruction again
* Example using `ADD` instruction
  * Fetch and decode the `ADD` instruction
  * Fetch `A`
  * Fetch `B`
  * Add `A` and `B`
  * Store the sum in `C`
  * If storing the sum in `C` faults because `C` is in a page that is not currently in memory, need to get the desired page, allocate it to a physical frame, update the page table, and restart the `ADD` instruction
  * Restarting the instruction involves fetching the instruction again, decoding it again, fetching the two operands again, and then adding again
