# CPU Caches

* In the 70s/80s, the frequency of the CPU core was comparable to memory
* In the early 90s, the frequency of CPU cores increased disproprotionally to the frequency of RAM chips
  * Creating RAM that is as fast as current CPU cores would be orders of magnitude more expensive
* If the choice is between a machine with ver little, very fast RAM and a machine with a lot of relatively fast RAM, the second will always win given a working set size which exceeds the small RAM size
  * The speed of secondary storage, usually hard disks, which must be used to hold the swapped out part of the working set has an access time that is orders of magnitude slower than even DRAM access
* SRAM is used to make temporary copies of data in main memory, which is likely to be used soon by the processor
  * Program code and data has temporal and sptial locality, meaning that over a short period of time, there is a good chance that the same code or data gets reused
  * For code, this could be loops, so that the same code gets executed over and over again (spatial locality)
