# DRAM Access

* A program selects a memory location using a virtual address
  * The processor translates this into a physical address
  * The memory controller selects the RAM chip corresponding to that address
* DRAM cells are organized in rows and columns
  * When reading, the content of all cells is made availble to the column multiplexer
    * The multiplexer produces two address lines that are used to produce the content of one column
    * This column is made available to the data pin of the DRAM chip
    * This happens many times in parallel on a number of DRAM chips to produce a total number of bits corresponding to the width of the data bus
  * When writing, the new cell value is put on the data bus
  * The cell is selected using the row address selection multiplexer and the column address selection multiplexer
* Note that DRAM chips have multiplexed the physical address of memory
  * The first part consists of two sets of address bits, which select the row
  * The second part consists of two sets of address bits, which select the column


