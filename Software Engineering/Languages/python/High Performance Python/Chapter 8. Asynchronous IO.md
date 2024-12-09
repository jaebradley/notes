# Chapter 8. Asynchronous I/O

* Getting the relevant data to your program can be the bottleneck, as opposed to the actual program execution speed
  * Program is I/O bound
* Every time file is read, or network socket is read/written, kernel is needed for interacting with hardware
* I/O operations are orders of magnitude slower than CPU
* So even if kernel is fast, have to wait for kernel to get result from device and return to caller
* Writing to network socket takes ~1ms, which could be millions of instructions on a modern CPU
  * Program is paused for much of this 1ms time (I/O wait) waiting for a signal that the write operation has completed
* Can execute multiple tasks on a single thread that all have I/O wait times concurrently in a way where while one task is waiting another task is executing
* Use mechanism called the event loop to continue performing computation operations and be notified when data is ready to be read

## Introduction to Asynchronous Programming

* When a program enters I/O wait, the excution is paused so that the kernel can perform the low-level operations associated with the I/O request
  * The program is not resumed until the I/O operation is completed
* This context switching is an expensive operation
  * Requires saving the state of the program
  * Loses CPU-level caches
* Event loop manages what runs in program
  * Effetively a list of functions that need to execute, and the function at the top of the list is executed, then next function, etc
  * Works best with asynchronous I/O operations that return immediately
  * When asynchronous operation finishes in the future, an event is fired and program will handle event
