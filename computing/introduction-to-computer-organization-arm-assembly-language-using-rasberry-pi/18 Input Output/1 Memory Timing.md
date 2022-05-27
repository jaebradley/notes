# [18.1 Memory Timing](https://bob.cs.sonoma.edu/IntroCompOrg-RPi/sec-memt.html)

* Two types of RAM in computers: SRAM and DRAM
  * SRAM holds values as long as power is on, with very fast access times, though is more expensive than DRAM
  * DRAM holds data values for only a few fractions of a second, and refreshes data values before the values are lost. Slower and cheaper than SRAM
* Cache Memory is a small amount of fast memory placed between the CPU and main memory
  * When the CPU needs to access a byte, if it is not already in cache memory, that byte, along with surrounding bytes are copied from main memory into cache memory
  * If a byte is accessed it is likely that surrounding bytes will also need to be accessed soon, and the CPU can work with values in the much faster Cache Memory
* Modern CPUs include cache memory on the same chip, which can be accessed at CPU speeds
