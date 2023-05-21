# 12.7.3 RAID Levels

* Level 0 - disk arrays with block striping, but no mirroring or parity bits
* Level 1 - disk mirroring

## Level 2

* Parity bits are used to identify whether or not the number of 1 bits in a byte set are even or odd
* If a bit in the byte set becomes damaged, the parity of the byte changes
* If the parity bit is damaged, it won't match the computed parity

## Level 3

* Disk controllers can detect whether a sector has been read successfully
* A single parity bit can be used for error correction and error detection
  * If a sector is damaged, we know what sector / disk it is
  * Using the stored parity bit, and then computing the parity of the remaining bits, we can deduce whether the remaining bit was a `0` or a `1`
* Only one parity disk is needed
* Block striping will distribute reads and writes across multiple disks
  * Increases the transfer rate at the expense of supporting fewer I/O operations per second, as every disk has to participate in every I/O request
* Parity computation can lead to significantly slower writes than non-parity RAID arrays
  * RAID storage arrays include a hardware controller with dedicated parity hardware
  * Offloads parity computation from CPU to this hardware
  * The RAID array always has an NVRAM cache, which stores blocks while parity is computed and buffers writes from the hardware controller to the downstream disks

## Level 4

* Uses block-level striping
* Keeps a parity block on a separate disk
* If one of the disks fail, the parity block can be used with the corresponding blocks from the other disks to restore the blocks of the failed disk
* Block reads only access one disk
  * Data transfer rates are slower than reading from multiple disks at a time
  * Multiple read accesses can proceed in parallel
* Blocks are read / written in parallel for large reads and large writes
* Small independent writes cannot be performed in parallel
  * Writing data smaller than a block requires that the block is read, modified with the new data, and written back
  * Parity block needs to be updated as well
  * Four disk accesses - two to read the data block and the parity block, and two to write the new data block and the new parity block

## Level 5

* Data and parity blocks are spread across `N + 1` disks
* If an array has 5 disks, the parity for the `n`th block is stored on disk `n mod 5` + 1
  * The data blocks are stored on the `n`th block on all the other 4 disks
* This setup avoids storing the parity information on the same disk as the data
  * Disk failure would result in loss of data and loss of parity
* Most common parity RAID system
