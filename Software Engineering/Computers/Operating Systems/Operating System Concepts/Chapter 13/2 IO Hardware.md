# 13.2 I/O Hardware

* Device communicates with a machine via a connection point
* Devices use a common set of wires - this connection is called a bus
  * Bus is a set of wires and a rigidly defined protocol
  * Protocol specifies a set of messages that can be sent on the wires
  * Messages conveyed by patterns of electrical voltages applied with defined timings
* A controller is a collection of electronics that can operate a port / bus / device
  * Controller has one or more registers for data and control signals
  * Processor communicates with the controller by reading and writing bit patterns in these registers
  * Special I/O instructions that specify the transfer of a byte or word in an I/O port address
    * These instructions trigger bus lines to select the proper device and to move bits into or out of a device register
* Memory-mapped I/O is when device-control registers are mapped into the address space of the processor
  * CPU executes I/O requests using the standard data-transfer instructions to read and write to the device-control registers
  * Used for controllers that require high-throughput (writing millions of bytes to memory is faster than issuing millions of I/O instructions)
