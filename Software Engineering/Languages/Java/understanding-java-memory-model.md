# [Understanding Java Memory Model](https://medium.com/platform-engineer/understanding-java-memory-model-1d0863f6d973)

## Heap Memory

* Heap is divided into 2 parts - the "Young Generation" and "Old Generation"
* Heap is allocated when JVM starts up

### Young Generation

* Reserved for containing newly-allocated objects
* "Eden Memory" and two "Survivor Memory Spaces"
* Most of the newly-created objects goes to Eden space
* When Eden space is filled with objects, Minor Garbage Collection (Young Collection) is performed and all survivor objects are moved to one of the survivor spaces
* After Minor GC, one of the survivor spaces is always empty
* Objects that survive many cycles of garbage collection are moved to Old generation memory space

### Old Generation

* When Old Generation space is full, Major Garbage Collection aka Old Collection performed (which usually takes more time)
