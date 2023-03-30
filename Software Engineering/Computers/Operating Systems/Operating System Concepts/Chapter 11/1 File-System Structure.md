# 11.1 File-System Structure

* Disks can be rewritten in-place
  * Read a block from a disk
  * Modify the block
  * Write the block back to the same place on the disk
* Disks can directly access any stored block of information
  * Sequential or random access is a matter of moving the read/write heads and waiting for the disk to rotate
* I/O disk transfers are usually in units of blocks
* Each block is comprised of 1+ sectors
  * Sector sizes can range from 32 bytes to 4kb
  * Usually 512 bytes
* File system is composed of layers
  * From lowest level to highest
  * I/O control consists of device drivers and interrupt handlers to transfer information between main memory and disk
    * Lowest level
    * A device driver can be thought of as a translator
    * Its input consists of high-level commands ("retrieve block 123")
    * Its output consists of low-level hardware-specific instructions, used by the hardware controller, which interfaces the I/O device to the rest of the system
    * Device driver usually writes specific bit patterns to special locations in the I/O controller's memory to tell the controller which device location to act on and what actions to take
  * Basic file system layer issues commands to the appropriate device driver to read/write physical blocks on disk
    * Physical blocks have numeric disk addresses (drive 1, cylinder 73, track 2, sector 10)
    * A block in buffer memory is allocated before the transfer of a disk block can occur
    * When buffer is filled, buffer manager must find more buffer memory or free up buffer space to allow the requested I/O to complete
  * File organization module layer translates logic block addresses to physical block addresses for the basic file system layer
    * Each file's logic blocks are numbered from 0-N
  * Logical file system layer manages metadata information (i.e. all the file-system data _except_ the actual contents / data in the files)
    * A file control block contains information about the file, like ownership, permissions, and location of file contents
