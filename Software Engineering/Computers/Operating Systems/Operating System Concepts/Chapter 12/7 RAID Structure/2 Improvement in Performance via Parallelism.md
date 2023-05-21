# 12.7.2 Improvement in Performance via Parallelism

* Disk mirroring doubles read throughput as any read request can be sent to one of the two disks
* Data striping splits the bits of each byte across multiple disks
  * If there are eight disks, then each bit for each byte can be sent to disk _i_
  * Every disk participates in each read and write
  * Each access can read 8 times as much data in the same amount of time on a single disk
  * Block-level striping is where blocks of files are striped across multiple disks
    * Most common form of data striping
