# Chapter 2: Memory Addressing

* Logical address is the address of an operand or an instruction
  * Each address consists of a segment and an offset - offset indicates distance from start of segment to actual address
* Linear address / virtual address
  * 32-bit unsigned integer and can be used to address up to 4GB (usually represented as hexadecimal)
* Physical address are used to address memory cells in memory chips
  * Correspond to the electrical signals sent along address pins of the microprocessor to the memory bus
  * Addresses are represented as 32-bit or 36-bit unsigned integers
* The memory management unit transforms a logical address into a linear address by means of a hardware circuit called a segmentation unit
* Then another hardware circuit called a paging unit transforms the linear address into a physical address
* In a multiprocessor system, all CPUs share the same memory
  * RAM chips may be accessed concurrently by independent CPUs
  * Because read / write on a RAM chip must be performed serially, a hardware circuit called a memory arbiter is inserted between the bus and every RAM chip
  * Memory arbiter grants access to CPU if chip is free and delays access if chip is servicing another processor's request

## Segmentation in Hardware

### Segment Selectors and Segmentation Registers

* A logical address has a segment identifier, which is a 16-bit field, and an offset that is a 32-bit field
* There are segmentation registers which hold segment selectors to make it easy to retrieve
  * For example, there is a stack segment register which points to a segment containing the current program stack

### Segment Descriptor

* Each segment is represented by an 8-byte segment descriptor (stored in the Global Descriptor Table or the Local Descriptor Table)
  * Usually only one GDT defined while each process has its own LDT
* There are various types of segments (and thus segment descriptors) - like those that refer to a code segment, a data segment, or a task state

### Fast Access To Segment Descriptors

* The segmentation registers only store the segment selectors - _not_ segment descriptors
* The 80x86 processor provides six additoinal nonprogrammable registers (i.e. cannot be set by programmer) that correspond to each of the segmentation registers
  * These nonprogrammable registers hold the 8-byte segment descriptor specified by the segment selector contained in the corresponding segmentation register
  * Thus, translations of logical addresses can be performed without referring to GDT or LDT in main memory
  * Only needs to access GDT or LDT when segmentation registers change
