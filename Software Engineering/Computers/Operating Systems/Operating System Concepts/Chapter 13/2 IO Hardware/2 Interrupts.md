# 13.2.2 Interrupts

* CPU hardware has an interrupt-request line that the CPU checks after executing every instruction
* If the CPU detects that a controller has asserted a signal on the interrupt-request line, the CPU performs a state save and jumps to the interrupt-hander routine at a fixed address in memory
* Interrupt handler identifies the cause of the interrupt, performing any necessary processing
* Interrupt handler performs a state restore
* Interrupt handler returns the CPU to the execution state prior to the interrupt

## Necessary Interrupt-Handling Features

* Defer interrupt handling during critical processing
* An efficient way to dispatch to the proper interrupt handler for a device without first polling all the devices to see which one raised the interrupt
* Multilevel interrupts - OS can distinguish between high-and-low-priority interrupts and can respond with the appropriate degree of urgency

## Interrupt Mechanism

* Accepts an address, an address that selects a specific interrupt-handling routine from a an interrupt handler table
* There are generally more devices, and thus, specific interrupt handlers, and addresses in the interrupt handler table
* Interrupt chaining changes the interrupt handler table into a map where each element in the table points to a list of interrupt handler
  * Each element in the list of handlers is called until an element is found that can handle the request

## Interrupt Prioirity

* At boot time, the OS introspects the hardware buses to determine which devices are present and installs the corresponding interrupt handlers into the interrupt table
* During I/O, device controllers raise interrupts when they are ready for service
  * Interrupts like output is complete, or that input data is available, or that a failure has occurred

## Interrupt Mechanism Use-Cases

* Many operating systems use the interrupt mechanism for virtual memory paging
  * Page fault = exception raising an interrupt
  * Interrupt suspends the current process and executes the page-fault handler in the kernel
  * Page-fault handler saves the state of the process
    * Moves the process to the wait queue
    * Performs page-cache management
    * Schedules an I/O operation to fetch the page
    * Schedules another process to resume execution
    * Returns from the interrupt
* System call implementation
  * System calls are executed by library methods using a software interrupt / trip
  * The trap has an operand that conveys the desired kernel service
  * When a process executes the traip, the interrupt hanrdware saves the state of the user code
    * Switches to supervisor mode
    * Dispatches the kernel routine that implements the requested service
* Control flow within the kernel
  * Example is a disk read
  * Need to copy data from kernel space to user buffer
    * Time-consuming but not urgent (should not block high-priority interrupt handling)
  * Need to also start the next I/O operation for the specific disk drive
    * Higher priority as if disks are used efficiently, should start the next I/O operation as soon as the previous one has completed
  * Thus, there are two interrupt handlers for disk reads
    * The high-priority interrupt handler records the I/O status, clears the device interrupt, starts the next pending I/O disk operation, and raises a low-priority interrupt to complete the work
    * When the CPU is not occupied with high-priority work, the low-priority interrupt will be dispatched
      * The corresponding handler completes the user-level I/O by copying data from the kernel buffer to the application space
      * Then calls the scheduler to place the application on the ready queue
