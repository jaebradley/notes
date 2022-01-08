# Caveats Of Using A TLB

* All threads and processes executed on the same processor core use the same TLB
* Since the translation of virtual to physical addresses depends on which page table tree is installed, the CPU cannot blindly reuse the cached entries if the page table is changed
* Each process has a different page table tree
  * The threads within the same process share the same page table tree
* Can flush the TLB whenever the page table tree changes, which is expensive
  * This occurs whenever a context switch occurs
    * This could be a switch from one thread / process to another, as it requires executing some kernel code
    * TLB flushes are restricted to leaving or entering the kernel address space / VMM address space
  * This may lead to unnecessary removal of entries since when the process is context switched to, the flushed TLB entries can be used again but are gone
  * For each entry into the kernel, the TLB has to be filled from scratch even though the page tables for the kernel and hypervisor usually do not change and the TLB entries could be preserved for a very long time 
    * This also explains why TLB caches are not bigger - programs most likely will not run long enough to fill all these entries
  * One optizimation is to individually invalidate TLB entries so that if the kernel code and data fall into a specific address range, only the pages falling into this address range have to be evicted from the TLB
* Add a unique identifier for each page table tree (i.e. a process's address space) as part of the tag used for the TLB access
  * This means that the TLB does not have to be completely flushed at all
  * The kernel, hypervisor, and the individual processes all can have unique identifiers
  * If the memory use (and thus, TLB memory use) of each runnable process is limited, the most recently used TLB entries for a process may still be in the TLB when it gets scheduled again
  * Special address spaces, like the kernel and VMM, are often only entered for a short time
    * After, control is returned to the address space that initiated the etnry
    * Without tags, one or two TLB flushes are performed
    * With tags, the calling address space's cached translations are preserved since the kernel and VMM address sapces do not often change TLB entries
    * When switching between two threads of the same process, no TLB flush is necessary. Without extending TLB tags, the entry into the kernel destroys the first thread's TLB entries
