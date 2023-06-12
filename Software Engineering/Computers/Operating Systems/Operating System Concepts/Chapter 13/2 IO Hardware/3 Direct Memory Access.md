# 13.2.3 Direct Memory Access

* For devices that do large data transfers (like a disk drive) it is wasteful to use Programmed I/O
  * Programmed I/O is when an expensive general-purpose processor is used to to watch status bits and feed data into a controller register one byte at a time
* Special-purpose processor called a direct-memory-access controller

## DMA Transfer

* Host writes a DMA command block into memory
  * Block contains pointer to source of transfer
  * Pointer to destination of transfer
  * Number of bytes to transfer
* CPU writes address of the command block to the DMA controller, and then moves on to other work
* DMA controller operates on the memory bus directly
  * Places addresses on the bus and performs transfers without the help of the main CPU
* Handshaking between the DMA controller and the device controller is performed via the DMA-request and DMA-acknowledge wires
  * Device controller places a signal on the DMA-request wire when a word of data is available for transfer
  * DMA controller then seizes control of the main memory bus
    * During this period of time, the CPU is prevented from accessing main memory
      * CPU can still access data in primary and secondary caches
    * Places the desired address on the memory-address wires
    * Places signal on the DMA-acknowleged wire
  * When the device control sees the DMA-acknowledge signal, it transfers the word of data to memory
    * Removes the DMA-request signal
* When the transfer is complete, the DMA controller interrupts the CPU to signal that the transfer completed
