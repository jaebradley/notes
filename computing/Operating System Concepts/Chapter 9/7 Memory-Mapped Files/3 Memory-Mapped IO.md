# 9.7.3 Memory-Mapped I/O

* Each I/O controller includes registers to hold commands and the data being transferred
* Many computer architectures provide memory-mapped I/O where ranges of memory addresses are set aside and are mapped to device registers
* Reads and writes to these memory addresses cause the data to be transferred to and from the device registers
  * For example, in the IBM PC, each location on the screen is mapped to a memory location
  * Displaying text on the screen is almost as easy as writing the text into the appropriate memory-mapped locations
* The CPU transfers data through devices like a modem or a printer via device registers
* For example, to write a string of bytes though a memory-mapped serial port:
  * CPU writes one data byte at a time to the data register and sets a bit in the control register indicating that a byte is available
  * The device takes the data byte and clears the control register bit to signal that it is ready for the next byte
  * CPU either uses polling to watch the control bit, or receives an interrupt when the device is ready for the next byte
