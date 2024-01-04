# CHAPTER 2: BACK-OF-THE-ENVELOPE ESTIMATION

* Rough power of two estimations
  * `2^10` ~ 1 thousand, 1KB
  * `2^20` ~ 1 million, 1MB
  * `2^30` ~ 1 billion, 1GB
  * `2^40` ~ 1 trillion, 1TB
  * `2^50` ~ 1 quadrillion, 1PB
* Rough operation estimations
  * Main memory reference - 100 ns
  * Compress 1K bytes with Zippy - 10 microseconds (10,000 ns)
  * Send 2K bytes over 1 GBPS network - 20 microseconds (20,000 ns)
  * Read 1 MB sequentially from memory - 250 microseconds
  * Round trip within same datacenter - 500 microseconds
  * Disk seek - 10 ms (10 million nanoseconds)
  * Read 1 MB sequentially from network - 10 ms
  * Read 1 MB sequentially from disk - 30 ms
  * Roundtrip packet from CA to Netherlands - 150 ms (150 million nanoseconds)
* Memory is fast, disk is slow
  * So avoid disk seeks
  * Simple compression algorithms are fast
* Data centers are usually in different regions, so it takes time to send data between them
* 99% availability - 14.40 minutes per day, 3.65 days per year
* 99.9999% availability - 86.40 milliseconds per day, 31.56 seconds per year
