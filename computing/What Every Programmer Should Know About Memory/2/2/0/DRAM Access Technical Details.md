# DRAM Access Technical Details

* Synchronous DRAM (SDRAM) works relative to a time source
  * The memory controller provides a clock
  * The frequency of the memory controller determines the speed of the front side bus
  * The frequency of the front side bus is not actually this high - today's front side buses are double/quad-pumped meaning that data is transported two or four times per cycle
  * For SDRAM today, each data transfer consists of 64 bits / 8 bytes
  * The transfer rate of the FSB is therefore 8 bytes multiplied by the effective bus frequency (~6.4GB/s for a quad-pumped 200MHz bus) - this is the maximum speed that will never be surpassed


