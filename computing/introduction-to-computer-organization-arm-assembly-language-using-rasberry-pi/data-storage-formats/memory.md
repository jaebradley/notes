# 2.7 Memory

* Random Access Memory (RAM)
 * Once a bit / switch is set to either zero or one, it stays in that state until the control unit actiely changes it or the power is turned off
 * The control unit can both read the state of each bit and change it
 * Random access means that it takes the same amount of time to access any byte in the memory, not that randomness is involved
   * This is in constrast to memory that is sequentially accessible e.g. tape, where the length of time it takes to access a byte depends upon the physical location of the byte with respect to the current position of the tape
* Read Only Memory (ROM)
  * The bits are permanently set
  * The control unit can read the state of each bit but cannot change it
* You can think of memory as a very long array of bytes
* Each byte has a particular location (or address) within this array
* The address of a particular byte never changes - but the state of each of the bits in any given byte can be changed
* Two hexadecimal characters exactly specify the state of each of the eight bits in a byte
