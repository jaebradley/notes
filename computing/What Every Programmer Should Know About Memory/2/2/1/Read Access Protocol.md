# Read Access Protocol

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
