# [An Introduction To Swap Space on Linux Systems](https://opensource.com/article/18/9/swap-space-linux-systems)

* There are two basic types of memory in a typical computer
  * RAM is used to store data / programs while they are being actively used by the computer
    * RAM is volatile memory meaning that data stored in RAM is lost if the computer is turned off
  * Hard drives are magnetic media used for long-term storage of data and programs
    * Magnetic media is nonvolatile - data stored on a disk remains even when power is removed from the computer
    * The CPU cannot directly access the programs and data on the hard drive - it must be copied into RAM first
    * During the boot process, a computer copies specific operating system programs, such as the kernel, into RAM where it is accessed by the CPU

## Swap Space

* Primary function of swap space is to substitute disk space for RAM memory when RAM memory is exhausted
* Assume computer system with 8GB of RAM
  * If programs don't require the full 8GB of RAM, no swapping is required
  * However, imagine adding a large spreadsheet that fills all of the RAM
  * Without swap space, would have to stop working on the spreadsheet until you could free up RAM by closing down other programs
* The kernel has a memory management program that detects blocks of code that have not been used recently
  * The program swaps these pages of memory out to a special partition on the hard drive specifically designated for swapping
  * This frees up RAM and makes room for more data to be entered into the spreadsheet
  * These pages of memory swapped to the hard drive are tracked by the kernel's memory management and can be paged back into RAM if needed
* Virtual memory is the total amount of RAM + the total amount of swap space
