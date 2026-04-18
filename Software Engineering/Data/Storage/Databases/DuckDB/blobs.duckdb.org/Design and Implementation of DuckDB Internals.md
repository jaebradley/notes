# [Design and Implementation of DuckDB Internals](https://blobs.duckdb.org/slides/DiDi-02.pdf)
* Example processing is a 6 million row CSV file (~750 MB), sum the 5th column
* External SSD = 800 MB/s, 0.9 seconds
* NVMe SSD = 5 GB/s, 0.14 seconds
* DRAM = 21 GB/s, 0.03 s
* DuckDB = 0.002 seconds
* `awk` took ~1.6 seconds
  * ~1.4 seconds in application code (`user` output in `time`) and ~0.14 seconds in operating system (system calls)
* `awk` processes CSV file with a throughput of 471 MB/s
  * `awk` is CPU-bound
  * MacOS `awk` is about 10 times slower than GNU `awk`
* Python processes the CSV file with a throughput of 275 MB/s
  * Python is also CPU-bound
* C program processes the CSV file with a throughput of 1.5 GB/s
  * Still CPU-bound but closer to SSD read bandwidth
  * Reading the CSV file one line at a time is too slow
* Instead, read the entire CSV file into DRAM using a single system call
* `mmap` a contiguous block of memory that holds the contents of the entire disk file
* If file size exceeds available DRAM, operating system will page in file contents on demand
* Avoid byte-wise search for the new-line character, instead load 8 bytes / 64 bits at a time, and advance a pointer 8 bytes at a time
* Optimized C program now processes CSV file with a 2.8 GB/s throughput
* Split the CSV file at new line boundaries into X chunks (partitions)
* Spawn `# of cores on machine` parallel threads, each summing the fifth column for each partition
* Add these thread-local partial sums to obtain an overall sum
* Threads use shared memory areas to exchange data
* Throughput is around 19 GB/s, approaching DRAM read bandwidth
