# 13.3.1 Block and Character Devices

* Interface for accessing disk drives and block-oriented devices
* Has a `read` and `write` command
* If the device is a random-access device, expected to have a `seek` command to specify which block to transfer next
* If the caller of the device performs its own buffering or locking, then having the device perform buffering/locking is redundant
  * Operating systems allow a mode of operation called raw or direct I/O

## Memory-mapped File Access

* The system call that maps a file into main memory returns the virtual memory address that contains a copy of the file
* Memory-mapped itnerface provides access to disk storage via an array of bytes in main memory
* Actual data transfers to retrieve on-disk data only occurs as-necessary
* To execute a program, OS maps the executable into memory
  * Transfers control to the entry address of the executable

## Character-stream Interface

* Keyboard is an example of a character-stream interface device
* Basic system calls allow callers to `get` or `put` one character at a time
* Libraries can be built that offer line-at-a-time access, with buffering and editing
