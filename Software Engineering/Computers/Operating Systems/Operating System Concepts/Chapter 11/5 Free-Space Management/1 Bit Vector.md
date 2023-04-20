# 11.5.1 Bit Vector

* Each free block is represented as a bit with value `1` and each allocated block is represented as a bit with value `0`
* If the second, third, and 5th blocks are free then the bitmap will look like `01101`
* Advantage of the bit vector is that it is relatively efficient to find the first free block or `n` consecutive free blocks using bit manipulation
* Sequentially check each "word" in the bit map to see whether that value is not `0`, since a `0`-valued "word" contains only `0` bits and represents a set of allocated blocks
* To find the first non-`0` "word", scan for the first `1` bit / location of the first free block
* Bit vectors are inefficient unless the entire vector is kept in main memory
  * Possible for smaller disk but not necessarily for larger ones
  * A 1-TB disk with 4-KB blocks requires 32MB to store its bit map
