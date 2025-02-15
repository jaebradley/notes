# [Understanding memory usage on Linux ](http://virtualthreads.blogspot.com/2006/02/understanding-memory-usage-on-linux.html)

## Why `ps` is "wrong"
* Reported memory information is the memory the process would require if it were the only process running
* Linux loads a single copy of shared libraries into memory and uses that one copy for every program that references it
* Two programs could therefore use a large shared library and yet have the library's size count towards both programs' memory totals

## Seeing a process's memory map
* Use the `pmap` program to see the memory used by a process
* Each shared library is listed twice - once for its code segment and once for its data segment
* Most of the largest memory allocations are code segments for shared libraries (mapping values that start with a `lib` prefix)
* The writeable/private total displayed by the `pmap` program is the incremental memory cost of the program (factors out shared libraries)

## What does it all mean?
* Pays to stick with one desktop's software as much as possible
* Use applications that will share libraries so the marginal memory cost of running a new application is as low as possible
