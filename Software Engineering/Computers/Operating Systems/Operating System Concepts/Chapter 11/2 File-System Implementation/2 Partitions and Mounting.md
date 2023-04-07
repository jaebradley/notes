# 11.2.2 Partitions and Mounting

* A disk can be sliced into multiple partitions
* Volume can span multiple partitions across multiple disks
* Each partition may (or may not) contain a file system

## Boot Partition

* Boot information can be stored in a separate / dedicated partition
* Boot information has its own format because at boot time, the computer system does not have the file-system code loaded and therefore cannot interpret the file-system format
* Boot information is usually a sequential series of blocks, loaded into memory
* Execution of these blocks starts at a predefined location (like at the first byte)
* Boot loader knows enough about the file-system structure to find and load the kernel
* Multiple operating systems can be installed
  * The boot loader understands multiple operating systems can occupy the boot space
  * The boot loader can boot one of the operating systems available on disk, spread across multiple partitions

## Root Partition

* Contains the operating-system kernel (and other system files)
* Mounted at boot time
* As part of mounting process, operating system verifies that the device contains a valid file system
* Operating system asks device driver to read the device directory and verify that the directory has the expected format
* Operating system notes in its in-memory mount table that a file system is mounted, along with the type of file system
* Windows-based systems mount each volume in a separate name space
  * Name space is denoted by a letter and a colon (like `F:`)
  * To record that a file system is mounted at `F:`, the operating system places a pointer to the file system in a field of the device structure corresponding with `F:`
  * When a process specifies the driver letter, the operating system finds the appropriate file-system pointer
  * The file-system's directory structure is traversed to find the specified file / directory
* UNIX allows file-systems to be mounted at any directory
  * "Mounting" involves setting a flag on the in-memory copy of the inode for that directory
  * Inode field points to an entry in the mount table, indicating the device mounted there
  * Mount table entry has a pointer to the volume control block / "superblock" of the file system on that device
