# 8.4.3 Protection

* Read-write / read-only bit in the page table used to verify that there are not any attempts to write to a read-only page
* Usually also a valid-invalid bit which indicates if an associated page is in a process's logical address space
  * If the bit is set to "invalid", the operating system disallows access to that page for a given process
* The valid-invalid bit + internal fragmentation due to paging can lead to cases where a page may be "valid" for a process, but the process does not have access to all the addresses in the page
