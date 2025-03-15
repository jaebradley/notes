# [IO devices and latency](https://planetscale.com/blog/io-devices-and-latency)

* Non-volatile storage “disk” stores binary data even when the computer it is attached to is powered off
* Volatile storage (CPU registers, CPU cache, RAM) are all faster than non-volatile storage but require continuous power to function

## Hard Disk Drives
* Data is stored on disks (“platters”)
* There is a read/write head, like the head of a record player
* The disk is divided into circular tracks
* Disk usually has over 100,000 tracks
* Each track contains hundreds of thousands of pages
* Each page usually contains 4kb of data
* It takes time to move the needle and/or wait for the disk to spin to the correct location for an operation
* Typical random read takes 1-3 milliseconds
* If an operation is requested before the current operation completes, the request is queued
* Disk processes requests in the order it receives them
* Due to the design of these disks, requests that require the least amount of movement from both the read/write head and the disk will be processed faster
* Performance improvements when a queue of operations builds up that the disk controller can then schedule to optimize for the geometry of the disk

## Solid State Drives
* Do not rely on any mechanical components to read data
* All data is stored and erased electronically using a special type of non-volatile transistor known as NAND flash
* SSDs are organized into one or more targets
* Each target contains many blocks
* Blocks contains some number of pages
* SSDs read/write full pages at a time
* Lines / wires exist between the SSD controller and targets
* Faster than HDD because no waiting around for data to spin to the read/write head
* SSD is 25000 times faster than a HDD

### Parallelism
* As mentioned before, each target has a dedicated line going from the control unit to the target
* This line processes reads and writes
* Only one page can be communicated by each line at any time
* Can “spread” out operations across multiple different targets and execute the different operations in parallel
* In comparison, if there were operations to the same target the operations would need to occur sequentially

### Garbage Collection
* The minimum chunk of data that can be read from/written to an SSD is the size of a page
* A page cannot be overwritten with new data until the previous data has been explicitly erased
* Individual pages cannot be erased, the whole block must be erased
* A dirty page is a page that has been written to but the data is no longer needed and ready to be erased
* The SSD will reorganize dirty pages across different targets and group dirty pages together so it can safely erase blocks of data (blocks are made up of pages)

## Local vs. network storage
* CPU <> RAM round trip of ~100 nanoseconds
* CPU <> locally-attached NVMe SSD takes ~50k nanoseconds (~5000 times slower)
* Still need disk because RAM is expensive and we need to store our data somewhere permanent
* Network-attached storage volume (like EBS)
  * Network round trip within a data center
  * Total time is around 250k nanoseconds - 5 times slower than locally attached NVMe
* Many cloud providers limit the amount of IO operations sent over a network
  * A GP3 EBS instance allows 3k IO operations per second
* Storage attached directly to the compute instance does not run into these limits