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
