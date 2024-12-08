# Chapter 8. Asynchronous I/O

* Getting the relevant data to your program can be the bottleneck, as opposed to the actual program execution speed
  * Program is I/O bound
* Every time file is read, or network socket is read/written, kernel is needed for interacting with hardware
* I/O operations are orders of magnitude slower than CPU
* So even if kernel is fast, have to wait for kernel to get result from device and return to caller
* Writing to network socket takes ~1ms, which could be millions of instructions on a modern CPU
  * Program is paused for much of this 1ms time (I/O wait) waiting for a signal that the write operation has completed

