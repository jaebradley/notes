# 9.4.4 LRU Page Replacement

* If we use recent past as an approximation of the near future, then we should replace the page that has not been used for the longest period of time
* LRU associates each page to the time of the page's last use
* Reference string of `7 0 1 2 0 3 0 4 2 3 0 3 2 1 2 0 1 7 0 1`
  * Assume can only keep 3 frames in memory at any time
  * The first 4 values are page faults - they aren't in memory (`7`, `0`, `1`, and `2`)
  * The fifth value, `0` is already in-memory
  * The sixth value, `3` is not in-memory - the third / least recently-used / referenced page is page `1`, so it is replaced by page `3`
  * The 7th value references `0` again
  * This pattern continues for a total of 12 page faults (vs. 15 page fautls for the same reference string when implementing FIFO Page Replacement)

## Implementations

### Counters

* Associate each page-table entry with a time-of-use field
* CPU contains a counter / clock register, which is incremented for every memory reference
* When a reference to a page is made, the contents of the clock register are copied to the time-of-use field in the page-table entry for that page
* Replace page with the smallest time value, which requires a search
* Write to memory for every memory access, to set the time-of-use field value

### Stack

* Whenever a page is referenced, it is removed from the stack and put on the top of the stack
* The top of the stack is the most-recently referenced page
* The bottom of the stack is the least-recently referenced page
* Implemented using a doubly-linked list with a tail and head pointer
* Worst-case update requires changing six pointers
  * Next element pointer for previous element
  * Previous element pointer for next element
  * Previous element pointer for current head element
  * Head element pointer (which should point to the newly-referenced element)
  * Previous and next element pointers for newly-referenced element
* Each update is expensive, but no need to search entire list for replacement - just evict bottom element in the stack
