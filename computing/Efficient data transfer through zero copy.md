# [Efficient data transfer through zero copy](https://developer.ibm.com/articles/j-zerocopy/)

* Reading data off disk and writing the data to a response socket (like when serving static content) is inefficient
  * Kernel reads data off disk and pushes it across the kernel-user boundary to be written out to the socket
  * Each time the data traverses the user-kernel boundary, it must be copied, using CPU cycles and memory bandwidth
* Zero copy is used to eliminate these extraneous copies letting the kernel copy data directly from the disk file to the socket

## Data transer: The traditional approach

```java
File.read(someFileDescriptor, buffer, length);
Socket.send(socket, buffer, length);
```

* Requires four context switches between user mode and kernel mode
  * Switch when executing the read syscall (switch back to the application before `Socket.send`)
  * Switch when executing the write syscall, when writing the buffer to the socket
* Requires four data copies
  * read data from file on disk, storing them into a kernel address space buffer
  * data is copied from the kernel address space buffer to the user address space buffer
  * the `Socket.send` call copies the data in the user address space buffer into a kernel address space buffer
    * Different kernel address space buffer associated with the destination socket
  * Final copy occurs when the kernel address space buffer data is passed to the protocol engine

## Data transfer: The zero-copy approach

* No need to read data into user-space only to immediately write the data to the socket buffer
* Instead, the data could be transferred directly from the read buffer to the socket buffer
* `transferTo` method (which calls the `sendfile` system call in UNIX) transfers data from one file descriptor to another
* So now two context switches (enter kernel space and return to user space during the transfer)
* Three copies 
  * copy file to read buffer
  * copy read buffer into kernel buffer associated with output socket
  * copy socket buffer to the protocol engine
* If network interface card supports gather operations
  * File contents are copied into kernel buffer
  * Descriptors with the location / length of data are appended to the socket buffer - no file data is copied into the socket buffer
  * DMA engine passes this data to the protocol engine
* In article's tests, using `transferTo` can decrease copy time by 65% compared to "normal" file transfer
