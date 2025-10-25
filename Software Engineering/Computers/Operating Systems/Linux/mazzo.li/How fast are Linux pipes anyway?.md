# [How fast are Linux pipes anyway?](https://mazzo.li/posts/fast-pipes.html)
* `perf` is a Linux kernel built-in program to measure where a program is spending time
* A Linux pipe is a ring buffer that holds references to pages where data is written to and read from
* Default number of slots for a ring buffer is 16
  * Thus, every pipe has an upper bound of the total amount of data it can hold before it is full
* The `head` node stores the write-end of the pipe
  * Writers will write into the buffer pointed at by `head` and increase the `head` reference accordingly if they need to write into the next buffer
  * Within a given write buffer the `len value stores how much has been written into the buffer
* The `tail` node stores the read-end of the pipe
  * Readers stat consuming the pipe from there
  * The `offset` value indicates where to start reading from
* Any unused buffers are `NULL`
* If a pipe is full (i.e. no `NULL`s and no page space) `write` will block
* If the pipe is completely empty (all `NULL`s) `read` will block
* When writing files and them reading them, each page of data is copied twice
  * Once from user memory to kernel memory when writing, and once from kernel memory to user memory, when reading
  * Working with non-contiguous memory, as there is constant page allocation
  * Acquiring and releasing the pipe lock

 ![image](https://github.com/user-attachments/assets/1d743306-24ed-4be1-95ef-4a8774a86370)
 
## Splicing to the rescue
* In general, writing to a socket, file, pipe means first writing to a buffer somewhere in the kernel
  * Pipes _are_ a series of buffers in the kernel
* Linux includes the `splice` and `vmsplice` system calls to speed things up when we want to move data to/from pipes without copying
  * `splice` moves data from a pipe to a file descriptor and vice-versa
  * `vmsplice` moves data from user memory to a pipe
* `vmsplice` returns how much was “spliced” into the pipe, which might not be the full amount, much like how `write` returns how much was written
  * Remember that pipes are bounded by how many slots they have in the ring buffer, and `vmsplice` is not exempt from this restriction
  * Since the user memory is moved to the pipe without copying, we must ensure that the read-end consumes it before we can reuse the spliced buffer
 
 ![image](https://github.com/user-attachments/assets/93f40ce3-60f5-4121-9b59-93c0f187a12b)

## A whirlwind tour of paging
* Processes do not refer to locations in RAM directly
* Processes are assigned virtual memory addresses, which get resolved to physical addresses
* Virtual pages map to physical memory pages
* So allocating 10k bytes via `malloc` will look contiguous in virtual memory, but that virtual memory will be mapped to physical pages that are not necessarily contiguous
* Kernel manages this mapping via a data structure called with page table
* There is one page table per process - each process has a reserved virtual address space
* When the kernel context switches to a process, it sets a special register (`CR3`) to the physical address of the root of this tree
* In the example address, the first level is the Page Global Directory (PGD)
  * In the example, the first 16 bits are unused
* Next 9 bits of the PGD to traverse down to the second level,the Page Upper Directory (PUD)
  * Next 9 bits are used to select an entry from the PUD
* Next two levels are the Page Middle Directory (PMD) and Page Table Entry (PTE)
  * PTE describes the actual physical page and the last 12 bits identify the offset within that physical page
 
 ![image](https://github.com/user-attachments/assets/86791a3a-477b-451e-b02f-98df145c1de0)

## The role of `struct page`
* `vmsplice` accepts virtual memory as input while `struct page` refers to physical memory

## The cost of getting pages
* `get_user_pages_fast` walks the page table to collect all the physical pages
* `get_user_pages_fast` needs to make sure the physical page is not repurposed until the caller doesn't need it anymore
* There is a reference counter stored on the `struct page` which is used to detect whether a physical page can be released and repurposed
* `get_user_pages_fast` must release the pages again with `put_page` to decrease this reference counter
* Kernel will try to get an already existing page table entry (relatively cheap operation)
* So `memset`ing the memory at the beginning will enusre that the page exists in the page table entry

## Huge pages
* Linux can allocate 2 MB "huge pages" like allocating memory aligned to 2 MB and then using `madvise` to tell the kernel to use huge pages for the provided buffer
  * Switching to huge pages yielded another 50% improvement
* Huges pages does not mean that `struct page` refers to a 2 MB page vs. a 4 KB page
* Huage pages / compound pages work via a 4 KB head `struct page` that contains information about the relevant physical page
  * Tail pages that reference the head page
  * 2 MB huge page = 1 head page and 511 tail pages
* Performance improvement comes from forgoing traversing the page table multiple times
  * Once the first entry is found, the following pages can be generated via a loop
