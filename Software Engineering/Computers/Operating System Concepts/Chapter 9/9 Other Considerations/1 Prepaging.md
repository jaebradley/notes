# 9.9.1 Prepaging

* Large number of page faults that occur when a process is started
  * Occurs when getting initial locality into memory
  * Occurs when a swapped-out process is restarted, all its pages are on the disk, and each must be brought in by its own page fault
* Instead bring into memory all the pages that will be needed
* In systems with the working-set model, each process has a list of pages
* If the process is suspended due to waiting for I/O, for example, the working set is stored for the process
* When the process is resumed, the entire working set is brought back into memory before restarting the process
* If many of the pages brought back into memory via prepaging are not used, the cost of the corresponding page faults may not be worth it
