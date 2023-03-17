# Memory Types

* Single Data Rate SDRAMs can output memory content at the same rate it can be transported over the memory bus
  * If the DRAM cell array can operate at 100MHZ, the data transfer rate of the bus of a single cell is thus 100MB/s
* Double Data Rate SDRAMs carry twice the amount of data than Single Data Rate SDRAMs
* The DDR1 chip transports data on the rising and falling edge (double-pumped bus)
  * Must introduce a buffer that holds two bits per data line
  * This requires that the data bus consists of two lines
* DDR2 doubles the frequency of the bus
  * Doubling the frequency means doubling the bandwidth
  * Since doubling the frequency is not economical for the cell array, it is now required that the I/O buffer get four bits in each clock cycle, which it can then send on the bus
  * This means that the changes to the DDR2 modules consist of making only the I/O buffer component of the DRAM capable of running at higher speeds
