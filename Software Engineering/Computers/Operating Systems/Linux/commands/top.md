# `top`

* Filter by `pid` using the `-pid` flag
* Can only ask for certain statistics using the `-stats` flag with comma-separated keys
* Example keys:
  * `CSW`: context switch count
  * Threads are represented as `(total number of threads/running threads)`
  * `ports (prt)`: number of Mach ports
  * `mregion (mreg, reg)`: number of memory regions
  * `mem`: Internal memory size
  * `rprvt`: Resident private address space size 
  * `purg`: Purgeable memory size
  * `vsize`: Total memory size
  * `vprvt`: Private address space size
  * `kprvt`: Private kernel memory size
  * `kshrd`: Shared kernel memory size
  * `ppid`: Parent process id
  * `state (pstate)`: Process state
  * `wq (workqueue)`:  Number of tasks on the workqueue (`total/running`)
  * `faults`: Number of page faults
  * `cow`: Number of `copy-on-write` faults
  * `sysbsd`: Total BSD system calls
  * `sysmach`: Total Mach system calls
* Default sorts by `pid`, but can sort by another key using the `-o` option like `-o mem` to sort by internal memory size

## Display at top of page

* `Disks` section shows number and total size of disk reads and writes
* `MemRegions` section shows number and total size of memory regions
* `Networks` section shows the number and total size of input and output network packets
* `PhysMem` section shows wired, active, inactive, used and free components
* `Procs` shows total number of processes and number of processes in each process state
* `Threads` shows number of threads
* `VirtMem` shows total virtual memory, vitual memory consumed by shared libraries, number of pageins and outs
* `Swap`: total size of swap area, amount of swap space in use, and amount of swap space available
* `Purgeable`: number of pages purged and number of pages currently puregable
