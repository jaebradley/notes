# Resident Memory vs. Virtual Memory

## From https://superuser.com/questions/618687/why-do-programs-on-linux-kernel-use-so-much-more-vmem-than-resident-memory

* Virtual memory is the sum of all virtual memory required by the process
* Resident memory is the amount of physical memory allocated to the process
* Physical memory used to hold virtual memory pages that are “resident” rather than located in a backing store (i.e. swapped out)
* Resident memory will be <= the process’s virtual memory size
* Up to OS to schedule processes and determine which pages stay resident in physical memory and which get swapped out
* Shared libraries are more likely to be kept resident
* A process in a sleeping state (`STAT == D1` or `STAT == S1`) could have most of its pages swapped out and have a small resident memory footprint (resident set size)
* Linux will overcommit requests for virtual memory. So even though no page frames or swap space is allocated for a process until the process tries to access this allocated memory, its virtual memory size can increase
* `top` documentation:
* `SWAP` - the swapped out portion of a task’s total virtual memory image
* `RES` - the non-swapped physical memory a task has used. code + data
* `VIRT` - the total amount of virtual memory used by the task. All code, data, shared libraries, plus pages that have been swapped out

## From https://serverfault.com/questions/138427/what-does-virtual-memory-size-in-top-mean

* If a process memory-maps a large file, the file is actually stored on disk, but still takes up process address space
* If a process calls fork, both processes share all the resident memory
* So if the resident memory was initially 1 GB, after forking there would be two processes, each with 1 GB of resident memroy, but in reality, still only using 1 GB of memory

## From https://unix.stackexchange.com/questions/35129/need-explanation-on-resident-set-size-virtual-size

* RSS is how much memory process has in main memory (RAM)
* If you only care about the heap size of a process, parse the `[heap]` entry in the `map` file in `/proc/$PID/map`

## From https://www.reddit.com/r/explainlikeimfive/comments/zkbue1/eli5_what_is_virtual_memory_what_is_it_good_for/

* As computers got faster there was a greater desire to multitask - run more than one program at a time
* There became a memory bottleneck because all the different programs would have to share memory (RAM)
* As hard drives became popular, operating systems began using the hard drive as a temporary memory storage area for programs
* It would be a little slower (to swap memory back and forth) but instead of the system running out of memory and a program failing, it would look like the machine had more RAM
* If you have 16 GB of RAM and 500 GB free on your hard disk, you “technically” have 516 GB of memory
