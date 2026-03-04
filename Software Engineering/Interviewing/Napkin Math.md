# [Napkin Math](https://github.com/sirupsen/napkin-math)
* Numbers are on an Intel Xeon E-2236 3.4 GHz with 12 virtual cores
* 1000 nanoseconds = 1 microsecond
* 1000 microseconds = 1 millisecond
* 1000 milliseconds = 1 second

## Throughput
* Reading 64 bytes of sequential memory: 0.5 nanoseconds
* Network data transfer in the same availability zone (inside VPC): 10 GiB/s
  * Outside VPC: 3 GiB/s
* Non-cryptographically-safe hashing of 64 bytes: 25 ns or 2 GiB / s
* Reading 64 bytes from random memory: 50 nanoseconds or 1 GiB / s
  * 100x slower than reading sequential memory
* System call: 500 nanoseconds
* Cryptographically-safe hashing of 64 bytes: 100 ns or 1 GiB / s
* Sequential SSD read of 8 KiB: 1 microsecond or 4 GiB / s
* Context Switch: 10 microseconds
* Sequential `fsync`ed SSD write of 8 KiB: 10 microseconds or 1 GiB / s
* TCP Echo Server (32 KiB packet): 10 microseconds or 4 GiB / s
* Decompression: 1 GiB / s
* Compression: 500 MiB/s
* Sorting 64-bit integers: 10 milliseconds or 250 MiB / s
* Sequential HDD (spinning hard drive) read of 8 KiB: 10 milliseconds or 250 MiB / s
* Blob Storage Read: 50 milliseconds or 500 MiB / s
* Blob Storage Put: 50 milliseconds or 100 MiB / s
* Multi-Part Blob Storage Put: 150 milliseconds
* Network data transfer within same region: 250 microrseconds or 2 GiB / s
* Random HDD (spinning hard drive) read of 8 KiB: 10 milliseconds or 0.7 MiB / s
* Network data transfer between EU West and NA East: 80 milliseconds or 25 MiB / s
* Network data transfer between NA West and Singapore: 180 milliseconds or 25 MiB / s

## Cost per month
* 1 CPU: $15 or $2 for spot instances
* 1 GPU: $5k or $1.5k for spot instances
* Blob: $0.02 for 1 GB
  * $0.40 for 1 million reads
  * $5.00 for 1 million writes
* Inter-availability-zone data transfer: $0.01 for 1 GB
* Inter-region data transfer: $0.02 for 1 GB
* Internet egress (sending data to S3 from GCP): $0.10 for 1 GB
* CDN egress: $0.05 for 1 GB
* Data warehouse query: $0.05 for 10 GB
* Logs/Tracers: $0.50 for 1 GB
* External Key Management: $1 per key

## Compresion Ratios
* HTML: 2-3x
* English: 2-4x
* Sourcecode: 2-4x
* Executables: 2-3x
* RPC: 2-10x
