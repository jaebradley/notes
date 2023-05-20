# 12.7.1 Improvement of Reliability via Redundancy

* The simplest and most expensive way of introducing redundancy is to duplicate every disk (mirroring)
* A logic disk consists of two physical disks (a "mirrored" volume)
  * Every write is executed on both disks
  * If one of the disks fails, data can be read from the other disk
  * Data loss only occurs if the second disk fails without the first disk being replaced
  * If a block is being written on both disks and a power failure occurs, the block can be in an inconsistent state across the two disks
    * Can do sequential writes
    * NVRAM cache is written to first
      * data is considered "written"
      * NVRAM keeps state even after power loss
      * NVRAM writes data to disks
* The independence of disk failures is not valid for natural disasters
  * Manufacturing defects and disk aging can cause correlated failures
