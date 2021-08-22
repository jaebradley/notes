# 1.3 How the Subsystems Interact

* A bus is a communication pathway with a protocol specifying exactly how the pathway is used
* A given bus implementation might not have physically spearate paths for different types of systems
  * For example, the PCI bus standard uses the same physical pathway for address and data, but at different times
  * Control signals indicate whether there is an address or data on the lines at any given time
* A program consists of a sequence of instructions that is stored in memory
  * When the CPU is ready to execute the next instruction in the program, the location of that instruction in memory is placed on the address bus
  * The CPU also places a "read" signal on the control bus
  * The memory subsystem responds by placing the instruction on the data bus, where the CPU can read it
* If the CPU is instructed to store data in memory, it places the data on the data bus, places the location in memroy where the data is to be stored on the address bus, and places a "write" signal on the control bus
  * The memory subsystem responds by copying the data on the data bus into the specified memory location
* If an instruction calls for reading or writing data from memory or to memroy, the next instruction in the program sequence cannot be read from memory over the same bus until the current instruction has completed the data transfer
* In modern computers, the bus connecting the CPU to external memory modules cannot keep up with the execution speed of the CPU (called the von Neumann bottleneck)
  * Modern CPUs include cache memory, which is connected to other CPU components with much faster internal buses
* CPU interactions with I/O devices is essentially the same as with memory
  * If the CPU is intstructed to read a piece of data from an input device, the particular device is specified on the address bus and a "read" signal is placed on the control bus
  * The device responds by placing the data item on the data bus
  * The CPU can send data to an output device by placing the data item on the data bus, specifying the device on the address bus, and placing a "write" signal on the control bus
