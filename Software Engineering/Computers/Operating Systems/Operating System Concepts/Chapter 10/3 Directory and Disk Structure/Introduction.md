# 10.3 Directory and Disk Structure

* Files are stored on random-access storage devices like hard disks, optical disks, and solid state disks
* A single file system can reside on a storage device, or the storage device can be partitioned and each partition can hold a file system
* Storage devices can also be collected into a RAID set that can be used to provide protection from the failure of a single disk
* Partitioning is useful for 
  * Limiting the size of individual file systems
  * Putting multiple file-system types on the same device
  * Leaving part of the device available for other use like as swap space
* A volume is an entity containing a file system
  * Volumes can store multiple operation systems
  * Each volume that cotnains a file system has a device directory that records the information for all the files on that volume
