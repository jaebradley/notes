# 11.7 Recovery

## 11.7.1 Consistency Checking

* Scan metadata on each file system
  * Occurs every time the system boots
  * Scan can take a long time
* Instead  of scanning, at the start of any metadata change, a status bit is set to indicate that the metadata is being updated
  * If all updates to the metadata complete successfully, the file system can clear that bit
  * If the status bit is set, a consistency checker needs to be executed
* `fsck` is Unix consistency checker
* Consistency checker compares data in the directory structure with data blocks on disk
* UNIX caches directory entries for reads
  * Any writes that result in space allocation or other metadata changes is done synchronously, before the corresponding data blocks are written
  * This is to avoid the loss of a directory entry in an indexed allocation system
  * Problems can still occur if the sycnhronous write is interrupted by a crash
