# [What Does a Database for SSDs Look Like?](https://brooker.co.za/blog/2025/12/15/database-for-ssd.html)
* Postgres, MySQL, etc were invented in the era of spinning disks
* NVMe SSDs have ~1000x improvement in both throughput and latency compared to spinning disks
* Design decisions like write-ahead logs, large page sizes, buffering table writes in bulk were due to slow disk I/O and when sequential I/O was orders of magnitude faster than random access
* The five minute rule (created in 1986) is that if a page was expected to be read again within five minutes, it should be kept in RAM
  * Otherwise, store it on disk
* EC2 `i8g.48xlarge` delivers about one dollar per billion reads
  * It has enough RAM for 50 million 32 KB pages
* Local SSD writes are only durable on the local box, so there is going to be off-box replication (most likely in a different availability zone)
* `i8g.48xlarge` has ~12 GB/s of network bandwidth, so upper bound on write throughput on a single-leader database
  * Cross-AZ latency in EC2 is a couple hundred microseconds to a couple milliseconds, so lower bound on commit latency
  * So this latency cost should be incurred at commit time and not during the actual write
* Avoid read-time coordination using multiversioning
* Offering isolation stronger than `READ COMMITTED` will require coordinating on write or commit time
* Aurora Postgres has a single leader at a time running in a single AZ
  * Great latency for clients in that AZ and worse latency for clients in different AZs
  * Given that most applications are hosted in multiple AZs, these latencies can add up, especially if there are many round trips to the database
* Aurora DSQL does cross-AZ round trips only at commit time
* Modern database built on SSDs will commit transactions to a distributed log, which provides multi-machine, multi-AZ durability
  * Recovery involves replaying the distributed log, from any number of the peer replicas
