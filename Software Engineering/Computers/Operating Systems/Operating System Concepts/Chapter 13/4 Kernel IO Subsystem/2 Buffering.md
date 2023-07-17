# 13.4.2 Buffering

* Buffer is a memory area that stores data being transferred between two devices (or a device and an application)

## Buffering occurs to cope with a speed mismatch betweeen the producer and consumer of a data stream

* File received by a modem for storage on a hard disk
* Modem is ~1000x slower than a hard disk
* Buffer created in main memory to accumulate the bytes received from the modem
* When buffer is full, write the entire buffer onto the hard disk in a single operation
* Since the disk write is non-instantaneous, and the modem still needs a place to store additional written bytes, two buffers are used

## Buffering is used to support copy semantics for application I/O

* Application has buffer of data that it wants to write to disk
* `write` system call, with pointer to the buffer and the number of bytes to write
* To avoid the application modifying the buffer between the start of the `write` system call and when the data is written onto disk, the application copies the buffer into a kernel buffer
  * Disk write is performed from the kernel buffer
