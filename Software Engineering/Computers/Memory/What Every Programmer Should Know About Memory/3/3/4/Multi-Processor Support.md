# Multi-Processor Support

* It is impractical to provide direct access from one process to the cache of another processor - the connection is simply not fast enough
  * Instead, the cache content is transfered to the other processor in case it is needed
  * This also applies to caches which are not shared on the same processor
* This transfer has to happen when one processor needs a cache line which is dirty in another processor's cache for reading and writing
  * Processor operations on cache lines are frequent, so broadcasting information about changed cache lines after each write access is impractical
* Modified, Exclusive, Shared, Invalid (MESI) protocol is named after the four states a cache line can be in when following the MESI protocol
  * Modified means that the local processor has modified the cache line - it is implied that this cache line is the only copy in any cache
  * Exclusive means that the cache line is not modified but known to not be loaded into any other processor's cache
  * Shared means that the cache line is not modified and might exist in another processor's cache
  * Invalid means that the cache line is unused
* Initially all cache lines are empty / Invalid
* If data is loaded into the cache for writing, the cache changes to Modified
  * If data is loaded for reading, the new state depends on whether anothe processor has the new cache line loaded
    * If loaded, then new state is Shared. If not loaded, then new state is Exclusive
* If a Modified cache line is read from or written to on the local processor, the instruction can use the current cache content
  * If a second processor wants to read from the cache line, the first processor has to send the content of its cache to the second processor and then it can change the state to Shared
  * If the second processor wants to write to the cache line, the first processor sends the cache line content and marks the cache line locally as invalid - this is an expensive operation
* If a cache line is in the Shared state and the local processor reads from it, no state change is necessary
* If the cache line is written locally, the cache line can be used, but the state changes to Modified and all other copies of the cache line in other processors is marked as Invalid via Request For Ownership (RFO) operation
* Unlike the Shared state, the Exclusive state does not have to announce a local write operation on the bus
  * The local cache is known to be the only one holding the specific cache line, which is advantagous, so the processor will tyr to keep as many cache lines as possible in the Exclusive state vs. the Shared state
* RFO messages are sent when a thread migrates from one processor to another and all the cache lines have to be moved over to the new processor once
  * A cache line is truly needed in two different processors
  * RFO messages are also necessary for distributing cache coherency protocol messages across all the processors of the system
  * MESI transitions cannot happen until it is clear all processors have had a chance to reply to the message
    * The longest possible time a reply can take determines the speed of the coherency protocol
* The FSB is a shared resource and in most machines, all processors are connected via a single bus to the memory controller
  * If a single processor saturates the bus, then multiple processors can restrict the bandwidth available to each processor
* AMD processors can have local memory and all processors can concurrently access their local memory quickly, but multi-threaded and multi-process programs have to at some points access the same memory regions to synchronize
