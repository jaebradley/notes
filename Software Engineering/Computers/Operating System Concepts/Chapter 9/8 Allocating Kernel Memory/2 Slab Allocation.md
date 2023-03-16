# 9.8.2 Slab Allocation

* A slab is made up of one or more physically contiguous pages
* A cache consists of one or more slabs
* A cache for each kernel data structure (a cache for process descriptors, a cache for semaphores)
* Objects allocated for the cache are marked as free
* When a new object from a kernel data structure is needed, the allocator can assign any free object from the cache to satisfy the request - the object is assigned from the cache and marked as used
* Slab has three possible states
  * Full - all objects are marked as used
  * Empty - all objects are marked as free
  * Partial - some objects are used and some are free
* Satisfy the request with a free object in a partial slab
  * If none exist, a free object is assigned from an empty slab
  * If no empty slabs exist, a new slab is allocated from contiguous physical pages and assigned to the cache
  * Memory is then allocated from the slab
* No memory due to fragmentation since the slabs are in chunks the size of the object
* Since the objects are created in advance, allocating and deallocating memory is quick
