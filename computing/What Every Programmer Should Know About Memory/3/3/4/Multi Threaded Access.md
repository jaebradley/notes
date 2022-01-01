# Multi Threaded Access

* Same test that walks linked list as before
  * So no RFO messages are necssary and all cache lines can be shared
  * Still see performance decrease when using multiple threads
  * Since no cache lines have to be transported between processors, this slowdown is caused by either the shared bus from the processor to the memory controller or the bus from the memory controller to the memory modules
* When modifying memory, there's a 93% penalty when running four threads, which means that the prefetch traffic combined with the write-back traffic is saturating the bus
* As more than one thread is running, the L1d cache is basically ineffective
  * The single-thread access times exceed 20 cycles only when the L1d is not sufficient to hold the working set
  * When multiple threads are running, those access times are hit immediately, even with the smallest working set sizes
* For the range of the L2 and L3 cache, using 2 or 4 threads achieves almost linear acceleration with respect to single-threaded performance
  * But as soon as the L3 cache is not sufficient to hold the working set, the numbers crash
  * The speed-up of two and four threads is identical, implying that scaling beyond two threads does not have much additional benefit
