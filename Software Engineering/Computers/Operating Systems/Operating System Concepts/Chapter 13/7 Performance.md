# 13.7 Performance
* I/O uses the CPU to execute device-driver code and to schedule processes fairly and efficiently as they block and unblock
* Context switches stress the CPU and its hardware caches
* Memory bus is used for copying data between controllers and physical memory and again during copies between the kernel buffers and the application data space
* Interrupts are expensive
  * Involve a state change
  * Execution of the interrupt handler
  * State restoration

## Network Traffic Example
* Remote login from one machine to another
* Each character typed on one machine must be transported to the remote machine

### Local Machine
* Character is typed on local machine
* This triggers a keyboard interrupts
* Interrupt handler passes character to the device driver, the kernel, and finally to the user process
 * Context switch occurs when character is passed from kernel to user process
* User process issues a network I/O system call to send the character to the remote machine
* Character is passed from the user process to the kernel (context switch)
* Kernel then passes character to the network layers that construct a network packet and into the network device driver
* Network device driver transfers packet to the network controller
* Network controller sends the character and generates an interrupt
* Interrupt propagates back up through the kernel to cause the network I/O system call to complete

### Remote Machine
* Remote machine's network hardware receives the packet and generates an interrupt
* Character is handed off to the appropriate network daemon
* Daemon identifies which remote login session is involved and passes the packet to the appropriate subdaemon for that session
* Usually receiver echoes character back to the sender (which doubles the work just described)

## I/O Systems
* Systems use separate front-end processors for terminal I/O
* A terminal concentrator can multiplex traffic from hundreds of remote terminals into one port
* I/O channel is a dedicated special-purpose CPU that is used to offload I/O work from the main CPU
