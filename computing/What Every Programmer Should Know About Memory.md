# [What Every Programmer Should Know About Memory](https://www.gwern.net/docs/cs/2007-drepper.pdf)

## Commondity Hardware Today

* Over the years, personal computers and smaller servers standardized on a chipset with two parts - Northbridge and Southbridge
  * All CPUs are connected via a common bus (the Front Side Bus) to the Northbridge
  * The Northbridge contains the memory controller (in addition to other things)
  * The Northbridge must communicate with the Southbridge
  * The Southbridge is referred to as the I/O bridge, handles communication with devices through a variety of different buses
* All data communication from one CPU to another, must travel over the same bus used to communicate with the Northbridge
* All communication with RAM must pass through the Northbridge
* The RAM has only a single port
* With limited bandwidth available, it is important for performance to schedule memory access in ways that minimize delays
  * As we will see, processors are much faster and must wait to access memory, despite the use of CPU caches
  * If multiple hyper-threads, cores, or processors access memory at the same time, the wait times for memory access are even longer
* One increasingly popular way to increase memory bandwidth is to integrate memory controllers into the CPU and attach memory to each CPU
  * One disadvantage to this architecture is that interconnects between processors have to be used when accessing memory attached to another processor

## RAM Types

### Static RAM (SRAM)

* One SRAM cell requires 6 transistors
* Maintaining the state of the cell requires constant power
* The cell state is available for reading almost immediately once the word access line is raised
* The cell state is stable, and no refresh cycles are needed

### Dynamic RAM (DRAM)

* Consists of one transistor and one capacitor
* The DRAM cell keeps its state in the capacitor
* The transistor is used to guard access to the state
* To read the state of the cell, the access line is raised, which causes a current to flow on the data line DL or not, depending on the charge in the capacitor
* To write to the cell, the data line is appropriately set and then the access line is raised for long enough to charge or drain the capacitor
* The use of a capacitor means that reading the cell discharges the capacitor
* The capacitor must be recharged at some point, and it must be recharged relatively often (once every 64 ms)
  * During a refresh cycle, no access tot he memory is possible since a refresh simply reads memory where the result is discarded
* Reading a cell causes the charge of the capacitor to be depleted
  * This means that every read operation must be followed by an operation to recharge the capacitor
  * This means that reading memory content requires additional energy and time
* Charging and raining a capacitor is not instantaneous
* The chip real estate needed for one DRAM cell is many times smaller than that of an SRAM cell
* The SRAM cells also need individual power for the transistors maintaining the state
* The structure of the DRAM cell is also simpler and more regular which means packing many of them close together on a die is simpler
* The cost difference wins - most hardware has main memory based on DRAM

### DRAM Access

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

## DRAM Access Technical Details

* Synchronous DRAM (SDRAM) works relative to a time source
  * The memory controller provides a clock
  * The frequency of the memory controller determines the speed of the front side bus
  * The frequency of the front side bus is not actually this high - today's front side buses are double/quad-pumped meaning that data is transported two or four times per cycle
  * For SDRAM today, each data transfer consists of 64 bits / 8 bytes
  * The transfer rate of the FSB is therefore 8 bytes multiplied by the effective bus frequency (~6.4GB/s for a quad-pumped 200MHz bus) - this is the maximum speed that will never be surpassed

### Read Access Protocol

* A read cycle begins with the memory controller making the row address available on the address bus and lowering the row address selection signal
* The column address selection signal can be sent after RAS-to-CAS delay clock cycles
  * The column address is then transmitted to making it available on the address bus and lowering the CAS line
  * The two parts of the address can be submitted over the same address bus
* The RAM chip needs time to prepare for data transmission after the addressing is complete (this delay is called the CAS Latency)
* With all this preparation to get to the data, it would be wasteful to only transfer one data word
  * DRAM modules allow the memory controller to specify how much data is transmitted
  * This allows for filling entire lines in the caches without a new RAS/CAS sequence
  * It is also possible for the memory controller to send a new CAS signal without resetting the row selection
    * Thus, this allows consecutive memory addresses to be read / written signficiantly faster because the RAS signal does not have to be sent again

### Precharge and Activation

* Before a new RAS signal can be sent, the currently latched row must be deactivated and the new row must be precharged
* The precharge signal has no dedicated line - some implementations issue it by lowering the Write Enable and RAS line simultaneously
* It takes the Row Precharge time / cycles until the row can be selected
  * This time overlaps with the memory transfer, which is good, but the precharge time takes longer than the memory transfer time, so the next RAS signal needs to wait some number of cycles
* One additional timing constraint is that SDRAM modules need time after a RAS signal before it can precharge another row
  * This time can be pretty high (2 to 3 times the Row Precharge time)
  * This can be a problem if after a RAS signal, only one CAS signal follows, and the data transfer is finished in a few cycles

### Recharging

* DRAM cells must constantly be refreshed
* At times, when a row is recharged no access is possible
* Each DRAM cells must be refreshed every 64 ms

### Memory Types
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

## CPU Caches

* In the 70s/80s, the frequency of the CPU core was comparable to memory
* In the early 90s, the frequency of CPU cores increased disproprotionally to the frequency of RAM chips
  * Creating RAM that is as fast as current CPU cores would be orders of magnitude more expensive
* If the choice is between a machine with ver little, very fast RAM and a machine with a lot of relatively fast RAM, the second will always win given a working set size which exceeds the small RAM size
  * The speed of secondary storage, usually hard disks, which must be used to hold the swapped out part of the working set has an access time that is orders of magnitude slower than even DRAM access
* SRAM is used to make temporary copies of data in main memory, which is likely to be used soon by the processor
  * Program code and data has temporal and sptial locality, meaning that over a short period of time, there is a good chance that the same code or data gets reused
  * For code, this could be loops, so that the same code gets executed over and over again (spatial locality)

### CPU Caches in the Big Picture

* Experience has shown that it is advantageous to separate the caches used for code and for data
* Processors hae multiple cores, and each core can have multiple threads
* Separate CPU cores have separate copies of almost all the hardware resources
  * The cores can run completely independently, unless they are using the same resource at the same time
  * Separate CPU cores have individual Level 1 caches, while threads share the same Level 1 cache
* Threads share almost all of the processor's resources

### Cache Operation at High Level

* By default, all data read or written by the CPU cores is stored in the cache
* If the CPU needs a data word, the caches are searched first
  * Each cache entry is tagged using the address of the data word in main memory (virtual or physical address)
* Since neighboring memory is likely to be used together, it should also be loaded into cache together
  * RAM moduels are more effective if they can transport many data words in a row without a new CAS / RAS signal
* When memory content is needed by the processor, the entire cache line is loaded into the L1 data cache
* The tag is also part of the memory address for each cache line
