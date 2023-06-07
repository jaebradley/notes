# 13.2.1 Polling

* Two bits are used to cordinate the producer-consumer relationship between the controller and the host
  * Busy bit is set by the controller
  * Host sets the command-ready bit
* Controller sets the busy bit to 1 when it is working and to 0 when it is ready to accept the next command
* Host sets the command-ready bit to 1 when a command is available for the controller to execute

## Handshake

* Host repeatedly reads the controller's busy bit until that bit is set to 0
* Host sets the write bit in the command register and writes a byte of data into the data-out register
* Host sets the command-ready bit
* When the controller notices the command-ready bit is set, it sets the busy bit
* Controller reads the command register and sets the `write` command
* Controller reads the data-out register to get the data byte and does the I/O to the device
* Controller sets the command-ready bit to 0
  * Controller sets the error bit to 0 to indicate that the I/O has succeeded
  * Controller sets the busy bit to 0 to indicate that the controller is finished working
* This handshake is repeated for each byte

## Polling

* In the first step of the handshake, the host is polling
* In some loop, it reads the status register over and over again until the busy bit is set to 0
* If the wait until the busy bit is set to 0 takes time, then the host should switch to another task
* For some devices, the host must stream data to the device quickly, or the data will be lost
  * When data is streaming from a keyboard, there is a small buffer on the controller
  * This buffer will overflow and data will be lost if the host waits too long before returning to read the bytes
* Polling becomes inefficient when it is attempted repeatedly but rarely finds a device ready for service (while other useful CPU processing remains undone)
  * More efficient to arrange for the hardware controller to notify the CPU when the device becomes ready for service
  * The hardware mechanism that enables a device to notify the CPU is called an interrupt
