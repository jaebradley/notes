# [Processes: A Brief Introduction](http://www.linfo.org/process.html)

* A process is an executing instance of a program
  * Proceses are also referred to as tasks
* A program is an executable file that is held in storage
* Storage referes to devices or media that can retain data for relatively long periods of time vs. memory whose contents can be accessed at extremely high speeds but which are retained only temporarily (i.e. while in use or only as long as the power supply remains on)
* An executable file is a binary file that has been compiled (i.e. converted from source code into machine code - a pattern of bytes that can be read directly by a CPU)
* Process can be thought of as a program in action
* An alternative definition of a process is the execution context of a running program - all of the activity in the current time slot in the CPU
  * A time slot also called a time slice or a quantum is the length of time that each process is permitted to run in the CPU until it is preempted / replaced by another process in a time sharing operating system
* Linux operating systems have time sharing capabilities which is made possible by the ability of the system to both retain many proceses in memory at the same time and switch between them fast enough to make it appear as though they are all running simultaneously
  * If one process crashes it will usually not cause other processes to crash because each process runs in its own protected memory space and is not capable of interacting with other processes except through secure mechanisms managed by the kernel (the core of the operating system)

## The Process Life Cycle

* When a computer is booted up, the operating system is loaded into memory
* The first part of the operating system is the compressed kernel executable
* In Unix-like operating systems each process is given a unique number (a PID)
* System calls are celarly defined, direct entry points into the kernel through which processes request services from the kernel
* If a process is suspended, it becomes eligible for swapping to a swap partition in order to free up space in the main memory for other processes

## Viewing Processes

* `ps` lists the currently running processes and their PIDs
  * Page forward using space and back using `b`

## Controlling Processes

* Starting a program in the background is accomplished by typing its name followed by an ampersand like `gftp &`
* A process that is running in the foreground can be suspended by simultaneously processing the CTRL + z keys and can be terminated by simultaneously pressing the CTRL + c keys
* `bg` reactivates a suspended program in the background
* `fg` puts a suspended program that is running in the background into the foreground
