# [Algorithms for Modern Processor Architectures](https://lemire.github.io/talks/2025/sea/sea2025.html#1)
* Disks can now read sequential data at gigabytes per second
* PCI Express Bandwidth
  * 2011: ~30 GB/s
  * 2017: ~60 GB/s
  * 2019: ~120 GB/s
  * 2022: ~240 GB/s
* Processors contain many GBs (example of Xeon Max processors containing 64 GB) of high-bandwidth memory
  * Bandwidth is 800 GB/s
* Processors at 4 GHZ = 1 cycle per 0.25 nanoseconds
  * If 1 byte is transferred per cycle, this means that processor can transfer 4 GB/s
* So data transfer is CPU-bound
* Processor frequency and transistors over time
  * 2000: 3.8 GHZ - 0.040 billion transistors (Pentium 4)
  * 2024: 5.7 GHZ - 50 billion transistors (AMD Zen 5)
* Transistors are used for caching, memory-level parallelism
  * This equal more instructions per cycle
* Transistors are used for data-level parallelism (SIMD)
  * This leads to fewer instructions
* Lemire's Rule 1: Modern processors exeute nearly as many instructions per cycle as you can supply
* Lemire's Corrolary 1: In computational workloads (batches), minimizing instruction count is critical for achieving optimal performance
* Thus: batch you work in larger units to save instructions + simplify processing to as few instructions as possible
* Massively reduced the number of CPU instructions in number parser, leading to 4x the performance of conventional alternatives
  * `strtod` = 1000+ instructions
  * their parser = ~200 instructions
* Apple M4 can learn 10k random branches
* SIMD = Single Instruction, Multple Data
  * Allows processing 16+ bytes with one instruction
  * Drastically lowers the cycles, instructions, and instructions per cycle
    * Example programs: Successive Difference (current value minus previous value), Prefix Sum (previous sum plus current value)
* Measurements (timings) are assumed to be normally distributed, but this may be an incorrect assumption
  * Absolute minimum is often a reliable metric and measurements may not be independent
