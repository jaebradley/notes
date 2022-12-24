# 9.6.2 Working-Set Model

* Working set window is the last X page references
* In other words, the set of pages in the most recent X page references is the working set
* If a page is no longer being used, it will drop from the working set X tiem units after its last reference
* The working set is an approximation of the program's locality
* Can think of the sum of all the working sets for each process in the system as the total demand for physical memory frames
  * If this total demand is greater than the total number of available frames, then thrashing will occur
* Operating system monitors working set of each process
  * If there are enough extra frames (after allocating existing process working-sets) then another process can be initiated
  * If the sum of the working set sizes (i.e. the "demand") exceeds the total number of available frames, the operating system selects a process to suspend
    * The process's pages are written out / swapped and its frames are reallocated to other processes
    * The suspended process can be restarted later
