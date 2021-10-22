# [Difference between physical addressing and virtual addressing concept](https://stackoverflow.com/questions/3243610/difference-between-physical-addressing-and-virtual-addressing-concept)

* Physical addressing means that your program actually knows the real layout of RAM - when you access a variable at address X, that's where it's really stored in the physical RAM chips
* Virtual addressing maps application memory to a physical address via a page table
  * Every applicaiton has its own "private" address space and no program can read or write to another program's memory - known as segmentation
* Benefits of virtual addressing is protecting programs from crashing into each other through poor pointer manipulation, etc.
* No pogram can read another's data
* A program's physical RAM can be stored on a disk when not being sued, and then called back when an application attempts to access the page
* Downside to virtual memory is added complexity in the hardware implementation and slower performance
