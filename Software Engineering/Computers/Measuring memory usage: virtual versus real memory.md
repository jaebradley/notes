# [Measuring memory usage: virtual versus real memory](https://lemire.me/blog/2021/07/29/measuring-memory-usage-virtual-versus-real-memory/)

* You may think that if you allocate an object that spans `32` bytes, your application might receive `32` bytes of real memory
* Operating systems allocate memory in units of pages - pages can be as small as 4kb
* Your application receives "real" memory in units of pages - you never just get "32 bytes" of memory from the operating system
  * Thus, a reasonable way to think about your memory usage is to count the pages that you access, with larger pages costing more
