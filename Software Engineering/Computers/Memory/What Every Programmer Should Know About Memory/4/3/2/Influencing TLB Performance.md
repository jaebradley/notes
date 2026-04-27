# Influencing TLB Performance

* Larger pages mean more instructions or data objects will fit into it
  * Reduces the overall number of address translations which are needed and fewer entries in the TLB cache are needed
* The memory regions used for these large pages must be contiguous in physical memory
* Each large page mustbe comprised of many smaller pages, which have to be contiguous physical memory
  * Allocating 2MB of contiguous physical memory with a unit page size of 4kB is challenging - requires free area with 512 contiguous pages
  * On Linux, allocate pages at system start time using `hugetlbfs` filesystem
  * Huge pages are the way to go in situations where performance is a premium, resources are plenty, and cumbersome setup is not a big deterrent (like database servers)
* Increasing the minimum virtual page size increases the size of memory mapping operations like loading applications
* If the page size is increased beyond what has been taking into account when the executable was built, the load operation cannot be performed
* Larger page sizes also mean that the number of levels of the page table tree is reduced
  * In the case of a TLB miss, the amount of work which has to be done is reduced
* It is possible to reduce the number of TLB entries needed by moving data which is used at the same time to fewer pages
  * Since the number of TLB entries is quite small, this can be an important optimization
