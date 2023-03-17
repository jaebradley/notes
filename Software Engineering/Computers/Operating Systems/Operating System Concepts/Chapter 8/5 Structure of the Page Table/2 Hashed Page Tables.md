# 8.5.2 Hashed Page Tables

* Key in the hash table is a hash of the virtual page number
* Each entry in the table is a linked list of elements that have the same hash value
  * The linked list is in case of collisions
  * Each element in the linked list consists of the virtual page number, the value of the mapped page frame, and a pointer to the next element
