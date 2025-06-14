# [Demystifying Debuggers, Part 2: The Anatomy Of A Running Program](https://www.rfleury.com/p/demystifying-debuggers-part-2-the)

* Programs are built to interact with virtual addresses, which are entirely independent from addresses in other virtual address spaces
* Virtual address spaces can be much larger than physical RAM limitations
* A thread of execution is comprised of state that is used to initialize the CPU to execute a sequence of instructions
* Executable image is a sequence of bytes that encode data in a platform-specific format. Contains encoded executable machine instructions, as well as relevant headers and metadata
* Loaders are parts of an operating system responsible for parsing and instantiating executable images such that the logic encoded in the images are executed
* Modules are loaded equivalents of executable images
* A process can load several modules
* A process is always initialized by loading one specific module, the initial executable image
* A process is an instance of a live, running program - contains several modules and threads of execution. Instantiated by the platform’s loader

## Virtual Memory
* Page tables map virtual addresses to physical addresses at page-size granularity
* Different systems have different page sizes (x86 Windows PC pages are 4KB, iPhone pages are 16KB)
* If a virtual address cannot be mapped to a physical address then a page fault exception is issued by the CPU and the thread of execution is interrupted
* If a page fault occurs while a program is executing, execution is transferred to the kernel. The kernel addresses this page fault exception.
* Operating systems can move memory allocated by one program to disk - “paging/swapping out” - if it expects that memory to not be accessed in the near future
* The now-available physical memory can be used to store more frequently accessed addresses
* If a page fault occurs when a program is attempting to access memory that has been paged out, i.e. stored on disk, the operating system moves that memory from disk back into physical memory, and then resumes execution
* Thus, thousands of programs can be executing simultaneously while the operating system manages efficient use of physical memory
* A 48-bit (6 byte) address space allows the representation of 2 ^ 48 distinct values - a large enough address space to refer to 256 terrabytes
* If pages are 4 KBs in size, then the last 12 bits of the 48-bit address will be identical between the virtual and physical addresses. 12 bits can represent 4 kilobytes
* The remaining 36 bits can be used to map from virtual to physical addresses
* Note that the page table only needs to map virtual addresses that have been allocated - initially, no virtual addresses are allocated
* Hierarchical structure that at the bottom level stores the page’s physical address
* Can slice the remaining 36 bits into 4 table indices each with 9 bits
* 2 ^ 9 = 512 so each table is comprised of 512 elements
* For each virtual address space, the operating system manages this page table structure so that memory reads and writes to the correct physical addresses are mapped via the appropriate virtual addresses
* Programs live in their own universe of virtual addresses, as if they had access to the entire system’s memory space and if that memory space far exceeded the limitations of a system’s RAM capacity
