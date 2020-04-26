# Item 44: Familiarize Yourself With Ruby's Garbage Collector

* Ruby GC uses mark and sweep
  * Object graph is traversed - objects that are reachable from code are considered alive and "marked"
  * Any objects that weren't marked but have memory associated with them are "swept" away and have their memory released back to Ruby and potentially back to the operating system
* Ruby 2.1 introduced "generational" garbage collection optimization
  * Objects are divided into two categories - young and old
  * Most objects probably don't live very long so if there's a long-lived object, garbage collector can optimize the mark phase by automatically marking old objects as reachable and skipping entire sections of the object graph when traversing
  * Young objects are considered "old" if they survive a marking phase
* Marking phase is split into two modes - major and minor
  * During major marking phase, all objects (young and old) are considered for marking - garbage collection is more expensive
  * During minor marking phase, only young objects are considered
  * So old objects can only be swept away in major marking phase
  * GC will prefer minor marking unless thresholds have been met that warrant full major marking
* Sweeping phase is split into immediate and lazy
  * In intermediate mode, the GC will free all unmarked objects
  * If there are a lot of objects, intermediate mode will be expensive
  * In lazy mode, GC will only free minimum number of objects possible
  * Each time a new object is created in Ruby, it will trigger a lazy sweeping phase to free up space
* Depending on OS, it can be expensive for application to request memory allocation from the free store
  * Ruby GC allocates more memory than immediately needed and maintain a memory pool, and only asking for more memory from OS when pool is empty
  * Ruby's memory pool is known as the heap (NOT to be confused with OS's free store, which is often times referred to as the heap)
    * Ruby's heap is divided into pages, which is further subdivided into slots where each slot is used to hold a single Ruby object
* When a Ruby app starts, it will allocate several pages and place them into the Ruby heap
  * When new objects are created, the garbage collector will first look for an empty slot to store the object
  * If it can't find a new slot, it will attempt a lazy sweeping phase in order to free one
  * If it still can't find an empty slot, the GC will allocate a new page and add it to the heap
  * When sweeping, if all slots in a page are released, the GC might return a page back to the OS's free store