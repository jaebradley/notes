# [How Discord Supercharges Network Disks for Extreme Low Latency](https://discord.com/blog/how-discord-supercharges-network-disks-for-extreme-low-latency)

## Scaling Beyond Our Hardware

* The biggest impact on Discord's (NoSQL) database performance is disk latency (how long it takes to read or write data from physical hardware)
* At a certain threshold, the database will need to wait for an outstanding disk operation to complete before it will issue another
  * Disks may take a millisecond or two to complete an operation, which causes a queue of disk operations that have yet to execute
* GCP provides Persistent Disk storage, which are not attached directly to a server, but connected nearby (probably same building) via network
  * Latency of the disk operations were on the order of a couple milliseconds vs. half a millisecond for directly-attached disks
* Local SSD / NVMe concerns - hardware issue with the disk or host, then the disks and their data are gone forever
  * Persistent Disks create point-in-time snapshots of an entire disk

## Creating the Super-Disk

* Write-through cache where Local SSDs are the cache and the Persistent Disks are the storage layer
* Local SSDs suffer from bad sectors, which can be repaired from the storage layer, but weren't able find a solution that had the capability / capacity
* `md` allows Linux to create software RAID arrays, turning multiple disks into one "array" / virtual disk
  * `md` offers a feature called "write-mostly", where individual devices / drives are excluded from normal read balancing and will only be read from when there is no other option
  * Discord requires at least 1TB or more of storage per database instance, while the Local SSDs are 375GB
  * Need to construct a bunch of smaller disks into one larger disk
  * `md` has RAID configurations which splits data across all disks
  * For example, RAID0 splits raw data across all disks and if one is lost, the entire array fails and all data is lost
  * Configuraiton is, RAID0 on top of the Local SSDs and a RAID1 between the Persistent Disk and RAID0 array
