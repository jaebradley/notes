# [Implementing LFU in O(1)](https://arpitbhayani.me/blogs/lfu)
* Most LFU caches use a min-heap which has a runtime complexity of `O(log n)` for insertions, updates, and deletions
  * Min-heap counts the counts of accesses for each element
* Constant time implementation uses a hash table and doubly-linked lists
* The hash table stores a mapping of the cache key to a value node, which holds the cached value
* Maintain a doubly-linked list of frequencies that holds one node for each unique frequency spanning cached values
  * This doubly-linked list is ordered from lowest frequency to highest frequency
  * Each node in the frequency doubly-linked list refers to a doubly-linked list of value nodes from the hash table
  * Each value node has a pointer to the frequency node it is associated with
  * So if value `A` and `B` have both been accessed `2` times (fewest), they would be in the lowest frequency node, which has a counter value of `2` and would have a `values` doubly-linked list with `A` and `B` 
* Adding a value to the cache involves
  * Checking to see if the value already exists in the hash table
  * If it does not already exist in the table, check to see if a frequency node of `1` exists
  * If not, one will need to be created
  * Create a value node object and add it to the frequency node of `1`'s `values` linked list and add the same value node object to the hash table
  * This should have constant runtime
* Evicting an item from the cache involves picking the frequency node with the lowest access and removing the first value node present in the `values` linked list
  * Also may involve deleting the frequency node if it's empty
* Getting a value from the cache involves
  * Checking the hash table to see if the key exists in the cache
  * Before returning the value (if the key exists in the cache), the cache accesses the frequency node pointer associated with the value node
  * Gets the next frequency node, since the frequencies are a doubly-linked list
    * Or creates a new frequency node, if none exists, to the doubly-linked list
  * Adds the value node to the next frequency node
  * May delete the previous frequency node, if it no longer has any value nodes
  * Value node needs to reference the new frequency node
 
<img width="1200" height="628" alt="image" src="https://github.com/user-attachments/assets/d3ea0d47-f6c6-42bd-b63a-09892b14fb2e" />
