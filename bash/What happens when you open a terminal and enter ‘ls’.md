# [What happens when you open a terminal and enter ‘ls’](https://www.warp.dev/blog/what-happens-when-you-open-a-terminal-and-enter-ls?utm_source=pocket_reader)

## History - From Teletypes to Terminal Emulators

* ASCII text transmitted over a physical wire from user to computer
* Kernel receives the characters and decode them
* Characters are sent to TTY driver
  * This kernel module is responsible for sending the user input to user programs and collecting the output
* Kernel sends output back to the teletype for display to the user
* Line discipline, buffered characters into kernel memory
  * Program wouldn't receive input until "Enter" was pressed
  * Performance optimization since asking the program to react to every individual character was inefficient

## Opening the Terminal App

* To service the full spectrum of use-cases (like writing scripts, conditional logic, running loops, etc) we want a full, interactive, interpreted programming environment
* A shell does the work of running other programs as processes and interpreting commands you write
  * Bash, Zsh, and fish are examples of popular shells
  * The terminal and shell are separate programs with separate concerns
    * The shell is concerned with the contents of the typed commands
    * The terminal is concerned with UI-related things like fonts, colors, tabs, scrolling
* Terminal needs to spawn a process for the shell the user wants, as well as a method for communicating with the shell and any processes the shell starts

## Creating a “PTY”

* Terminal emulators work by streaming characters that users type to file descriptors called PTY (pseudo-TTY)
  * There are actually pairs of file descriptors that represent the two ends of the physical wire that transmitted user-entered input to programs, which also sent back output
* Terminal asks the kernel to creat these files
* Kernel still has a TTY driver with a line discipline for mediating data between the two ends of the PTY
  * One end of the PTY is the "leader" and the other end is the "follower" (which is used by the shell and all other processes created in the session)
* The PTY file descriptors are virtual character devices
  * "leader" PTY file descriptor points to a buffer in memory
  * "follower" PTY file descriptor points to a character device file with an actual path on disk
