# [Tricks of the Trade: Tuning JVM Memory for Large-scale Services](https://eng.uber.com/jvm-tuning-garbage-collection/)

## What is JVM garbage collection?

* JVM runs on a local machine and functions as an operating system o the Java programs written to execute in it
  * It translates the instructions from its running programs into instructions and commands that run on the local operating system
* JVM garbage collection process looks at heap memory, identifies which objects are in use and are not, deleting unused objects to reclaim memory
* Young Generation is where all new objects are allocated
  * When the Young Generation fills up (i.e. using all its allocated memory) a minor garbage collection occurs
  * Minor garbage collections are "Stop the World" events, meaning that the JVM stops all application threads until the operation completes
* Old Generation stores long-surviving objects that have met some threshold age, where they are moved from the Young Generation to the Old Generation
  * When the Old Generation needs a major garbage collection, it's a full or partial "Stop the World" event, depending on garbage collection configuration
* GC pause time usually increases when the JVM heap size is increased
* New GCs try and solve this problem by minimizing pauses caused by running the collecting phases concurrently and incrementally more frequently than traditional collectors
 * The pause time does not increase as the heap size goes up

## Reduce object creation to improve API latencies

* Latency spikes are commonly due to upstream dependency degradataion, synchronization and queuing conflicts, JVM pauses due to GC
* Uber had an issue where there were a lot of very frequent short pauses
* GCs were in the Young Generation, and on average, the pause was 177 ms
* Indicates that many new objects were getting created too soon, and since Young Generation was not going to the Old Generation, indicates that too many objects were getting destroyed too soon
* High garbage creation rate indicates a busy application with too many incoming requests or in-efficient memory management
* The jstack utility can capture a snapshot of all threads with their call stack and state
* Thread dump showed very few scheduled threads in a runnable state, save for one thread, that was always in a runnable state
 * This thread was a metrics collector daemon that was repeatedly getting JVM vital metrics and publishing them to a server
 * The backoff for the thread was incorrectly configured to 1 millesecond (instead of 1 second) causing the metric analysis to occur 1k times per second, generating a lot of short-lived objects

## Preventing continuous full garbage collections

* Situation where full GCs resulted in very few bytes being reclaimed
* When each full GC failed to reclaim many bytes, it triggered another GC, etc., until out-of-memory exceptions were thrown
* Usually, consecutive GCs are caused by under-allocation of JVM heap size (less than what the application requires)
* Typically, the heap should use less than 75 percent of the available RAM for the JVM. Need to account for RAM of the operation system and other processes on the same host, and I/O buffering
* Verbose GC log contains the maximum memory footprint, which is the heap size when a full GC reclaims all the garbage
 * Typically, the total heap size should be larger than the maximum memory footprint - Uber set the total heap size to be 20% larger than this memory footprint value
