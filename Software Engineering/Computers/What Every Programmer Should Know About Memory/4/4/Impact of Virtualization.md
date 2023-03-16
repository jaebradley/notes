# Impact of Virtualization

* Technologies like Xen (i.e. virtualization of OS images) execute independent OS images by being the one piece of software which directy controls access to the physical memory
* VMM hands out physical memory to the Dom0 and DomU kernels, which implement the usual memory hanlding as if they were running directly on a processor
  * [Dom0](https://wiki.xenproject.org/wiki/Dom0) is the initial "domain" started by the Xen hypervisor
    * It is a privileged domain that starts first and manages the DomU unprivileged domains
    * Essentially the host operating system
* To implement the separation of the domains, the memory handling in the Dom0 and DomU kernels does not have unrestricted access to physical memory
  * The VMM does not hand out memory by giving out individual physical pages and letting the guest OSes handle the addressing - this would not provide protection against faulty or rogue guest domains
  * The VMM creates its own page table tree for each guest domain and hands out memory using these data structures - accessing this page table tree is controlled so any malicious code that tries to access this page table (assuming it does not have the approriate privileges) cannot do anything
* When the guest OS modifies its page tables, the VMM is invoked
  * The VMM then uses the updated information in the guest domain to update its own "shadow" page tables which are the page tables that are actually used by the hardware
  * This is an expensive process where each modification of the page table tree requires an invocation of the VMM
* Processors are strating to have additional functionality to avoid the creation of shadow page tables
  * The page tables of the guest OSes produce "host virtual addresses" from the "guest virtual address"
  * These "host virtual addresses" are then further translated using the per-domain page tables, into actual physical addresses
  * From https://en.wikipedia.org/w/index.php?title=Second_Level_Address_Translation&oldid=1060886694: "By treating each guest-physical address as a host-virtual address, a slight extension of the hardware used to walk a non-virtualized page table (now the guest page table) can walk the host page table. With multilevel page tables, the host page table can be viewed conceptually as nested within the guest page table. A hardware page table walker can treat the additional translation layer almost like adding levels to the page table."
  * These nested page tables allow memory handling at almost the speed of the no-virtualization case since most VMM entries for memory handling are removed
  * It also reduces the memory use of the VMM since now only one page table tree for each domain (vs. for each process) has to be maintained
* The KVM linux kernel extensions do not have a separate VMM running directly on the hardware and controlling all the guests - it instead uses a normal Linux kernel
  * Guest domains can run alongside the normal user-level processes in what is called "guest mode"
  * The virtualization functionality is controlled by the KVM VMM which is a user level process that uses a special KVM device the kernel implements
  * Unlike the Xen VMM implementation, the memory handler in a Linux guest OS makes the same assumptions as the memory handler in the outer Linux kernel, running on bare hardware, theoretically leading to less work, fewer bugs, less friction
* Cach misses (instruction, data, TLB) have a higher cost with virtualization than without virtualization
