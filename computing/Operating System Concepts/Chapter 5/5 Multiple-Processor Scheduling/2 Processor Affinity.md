# 5.5.2 Processor Affinity

* When a process runs on a specific processor, the process populates the processor's cache
  * Successive memory accesses by the process are often satisfied by the processor's cache memory
* If the process is migrated to another processor, the cache memory must be invalidated for the first processor
  * Cache memory for new processor must be repopulated
  * This invalidation / repopoulation has a high cost, so most symmetric multiprocessing systems avoid migrating processes between processors
  * Attempt to keep process running on same processor
* Linux supports hard processor affinity, which specifies a process must not migrate to other processors
* Non-uniform memory access (NUMA) architecture means that CPUs have faster access to some parts of main memory than to other parts
  * Usually occurs when CPUs and memory boards are combined (i.e. multiple separate boards where CPU and memory components are on the same board)
  * CPUs can access the memory that is on the same board faster than memory on a separate board
  * A process that is assigned affinity to a particular CPU can be allocated memory on the board where that CPU resides
