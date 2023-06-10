# 13.2.2 Interrupts

* CPU hardware has an interrupt-request line that the CPU checks after executing every instruction
* If the CPU detects that a controller has asserted a signal on the interrupt-request line, the CPU performs a state save and jumps to the interrupt-hander routine at a fixed address in memory
* Interrupt handler identifies the cause of the interrupt, performing any necessary processing
* Interrupt handler performs a state restore
* Interrupt handler returns the CPU to the execution state prior to the interrupt
