# 12.6.1 Swap-Space Use

* Virtual memory uses disk space as an extension of main memory
* Disk access is much slower than memory access, so swap space significantly decreases system performance
* Swapping can swap entire processes including code and data segments
  * Paging systems implement swapping by storing pages that have been pushed out of main memory
* Safer to overestimate swap space than underestimate
  * Underestimation can lead to processes needing to be aborted or crashing
  * In the past, Linux suggested setting swap space to 2x the amount of physical memory
* OSes like Linux also have multiple swap spaces
  * Swap spaces distributed across multiple disks so that load placed on the I/O system from paging / swapping can be distributed across the system's I/O devices
