# [Modern Garbage Collection](https://blog.plan99.net/modern-garbage-collection-911ef4f8bd8e)

* Garbage collection factors
  * How much does the garbage collection algorithm slow the program down
  * How much garbage can the collector clear given a fixed amount of CPU
  * How long does your collector stop the world for
  * How often does your collector stop the world
  * Typically have short pauses and then long pauses
* The first garbage collection algorithms were designed for uniprocessor machines and programs that had small heaps
  * They were designed to minize CPU and heap overhead
  * So the garbage collection did nothing until memory was no longer allocatable
  * Then the program would be paused and a faull mark / sweep of the heap would be done to mark parts as free as quickly as possible
  * Benefit of these algorithms is that they are simple, don't slow down the program when they are not collecting, and dont' add any memory overhead
  * The issue with stop-the-world mark/sweep algorithm is that it scales poorly - as cores increase, the heaps / allocation rates grow larger and pause times grow longer
* There are cases where low pause times are important - like trading in financial markets
  * In those cases, it's probably better to use an algorithm that actually slows down your program whilst it runs in order to do collection in the background with low pause times

## Generational Hypothesis

* Most allocations "die young" (i.e. become garbage very soon after being allocated)
* Allocations became netaly laid out in space next to each other (which improves caching)
* Implementing a generational requires the ability to move things around in memory, and do extra work when the program writes to a pointer in some cases - this means that garbage collection must be tightly integrated with the compiler

## The Go concurrent collector

* The Go concurrent collector is _not_ generational - it runs a plain old mark/sweep
* Main ebenefit is very low pause times - however there are some drawbacks like
  * Garbage Collection throughput - the time needed to clear the heap of garbage scales with the size of a heap
    * The more memory your program uses, the more slowly memory gets freed up, and the more time your computer spends doing collection vs useful work
  * Any garbage collector that runs concurrently with your program can encounter what the Java world calls a "concurrent mode failure" - your program creates garbage faster than the garbage collector threads can clean it up
  * Collecting the heap via mark/sweep very slowly, you need lots of spare space to ensure a concurrent mode failure does not occur
    * Go defaults to a heap overhead of 100% - it doubles the amount of memory your program needs

## JVM Garbage Collection Algorithms

* Concurrent mark/sweep collectors
  * Generational, unlike Go's collector
  * It has longer pause times than Go's - the young generation is compacted while the app is paused
  * The first / faster kind, lasts around 2-5 milliseconds
  * The second might be more like 20 milliseconds
  * Because it's concurrent, it has to guess when to start running
