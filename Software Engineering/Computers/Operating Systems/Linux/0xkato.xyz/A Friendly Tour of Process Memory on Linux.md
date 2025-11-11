# [A Friendly Tour of Process Memory on Linux](https://www.0xkato.xyz/linux-process-memory/)

## The floor plan you never see
* Inside the kernel, the process owns one object that represents the whole address space
* Mental model is that the address space is a floor plan
  * Each room on the floor plan is a virtual memory area
  * Rooms are contiguous ranges of memory with the same permissions
  * They also have the same persistence (anonymously mapped vs. file mapped)
* CPU uses page tables to translate virtual addresses to physical addresses
* All threads in the process share the same floor plan
* When the CPU scheduler runs a given process, the CPU references the process's page tables
* `mmap` reserves memory with permissions and a persistence source
  * "draws a room in the floor plan"
* `mprotect` changes the permissions of an existing memory range
  * "changes the sign on the room's door"
* `munmap` removes a mapping from a process's address space
  * "tears down a room"

## `mmap`, without the fog
* `mmap` is like drawing a promise on the floor plan
  * "Give me a range of addresses with these rights backed by this file or anonymous memory"
  * The kernel picks an address, makes sure it doesn't collide, and then records the promise
* `mmap` does not allocate pages
* Shared means that updates are visible to other processes that share the file and writes are carried through to the underlying file
* Private means that updates are not visible to other processes that share the file
  * Writes go to private copy-on-write pages and writes are not carried through to the underlying file

## The first touch
* CPU tries to translate an address, but finds no entry, so it raises a page fault
  * Page fault is the CPU asking the kernel to handle a missing or illegal translation for an address
* Kernel's handler asks three questions in the following order
  * Is the address inside any virtual memory area?
    * If the answer is "no", then there is a hole in the floor plan and there's a `SIGSEGV`
  * Do the rights allow this access?
    * Writing to a read-only page results in a `SIGSEGV`
  * If it is valid, but missing, make it real
    * For an anonymous mapping, the kernel allocates a zero-filled physical page
      * Wires a page table entry with the reqeusted permissions and returns to instruction
    * For file mapping, kernel first checks the page cache
      * If the page cache is not in RAM, it reads from storage, installs the translated page, and retries the instruction
* Minor fault means that the data is in RAM and the page table translation is missing
* Major fault means that the kernel had to wait for I/O
* User stacks have a guard page
  * Touching just below the current stack can grow it
  * Touching far below looks like a bug and gets a `SIGSEGV`

## Copy on write with `fork()` and `MAP_PRIVATE`
