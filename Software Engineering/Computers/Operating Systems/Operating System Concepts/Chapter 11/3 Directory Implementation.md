# 11.3 Directory Implementation

## 11.3.1 Linear List

* Implementation consists of a linear list of file names with pointers to the data blocks
* Simple to program, but ineffecient to execute
* To create a new file, have to search the directory to ensure that no existing file has the same name
* Also have to linear search the directory when deleting a file
* Often times, a sorted list is implemented which allows binary search and decreases the average search time

## 11.3.2 Hash Table

* Linear list stores directory entries
* Hash table hashes file name as its key and stores a pointer to the file name in the linear list
* Difficulties with the hash table are that it has a fixed size / the hash function is tied to that size
  * Example is hash table with 64 entries
  * Hashes based on remainder of division by 64
  * If a 65th file is created, then need to resize the hash table to 128 entries, and must need a hash function to map file names to numbers from 0 to 127
* Chained-overflow hash table can be used where each hash entry is a linked list instead of a value
  * Hash collisions are resolved by adding a new entry to the linked list
  * Lookups are a little slower than a "pure" hash table as the linked list will need to be iterated for hash collisions
  * At worst, will be as slow as a linear search
