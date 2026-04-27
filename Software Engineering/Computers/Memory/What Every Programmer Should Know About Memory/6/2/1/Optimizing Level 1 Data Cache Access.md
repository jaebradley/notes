# Optimizing Level 1 Data Cache Access

* Problem is matrix multiplication which can be implemented like this:

```c
for (i = 0; i < N; ++i)
  for (j = 0; j < N; ++j)
    for (k = 0; k < N; ++k)
      res[i][j] += mul1[i][k] * mul2[k][j];
```

* `mul1` is accessed sequentially, but the inner loop advances the row number of `mul2` and it's rows are not accessed sequentially
* Can transpose the second matrix, flipping the rows and columns so that the implementation of the calculation looks something like

```c
res[i][j] += mul1[i][k] * transposed[j][k];
```

* All of the rows in `transposed` should be accessed sequentially (note that the extra transposed matrix requires additional memory - in the problem, the matrix is "only" 1,000 x 1,0000)
  * Speed up is ~76%, again showing the penalty of non-sequential accesses
* Further optimization within the document where processing rows in the matrix use all bytes of a cache line, and the entire cache line is used before it is evacuated
  * This, combined with vectorization leads to a program that runs in 10% of the time of the original code
  * Vectorization are special instructions that allow processing 2, 4, 8 or more values at the same time. So in the matrix multiplication case, the Intel processors can handle two double values in one operation
* Experiment using program that adds elements in a list - in one case, the elements are in the same cache line, and in another case, on element is in the first cache line of the list element and the second element is in the last cache line of the element
  * No negative effects if the working set fits into the L1d
  * If working set does not fit into the L1d, then there are penalties by using two cache lines in the process instead of one
* The performance of code with the critical word late in the cache line is worse, so always move the structure element which is most likely to be the critical word to the beginning of the structure
  * When accessing the data structures, and the order of access is not dictated by the situation, access the elements in the order in which they are defined by the structure
* For small structures, this means that the elements should be arranged in the order in which they are likely accessed. For bigger data structures, each cache line-sized block should be arranged to follow the rules
* Even if the members of a structure are lined up to fit into the same cache line, an allocated object might not have an alignment matching the cache line size
* Experiment that increments a data element, while visiting memory (sequentially or randomly) shows a penalty when executed with deliverately misaligned elements vs. aligned list elements
  * The relative penalties for the sequential access case look higher than for the random access case because in the random access case, the costs of unaligned accesses are partially hidden by the generally higher costs of the memory access
  * For very large working set sizes, the effects of unaligned access is still 20-30%, which is a lot ogiven that the aligned access times for those sizes are large
* Compilers can keep alignment intact within a stack frame
  * Since the size of a stack frame used in a function is not necessarily a multiple of the alignment, this means padding is needed if other functions are called from this stack frame
  * The stack frame size is, in most cases, known to the complier and, therefore, it knows how to adjust the stack pointer to ensure alignment for any function which is called from that stack frame
  * Most compilers will simply round the stack frame size up and be done with it
* The L1d cache can see performance degradataion if many of the objects in the working set fall into the same cache set
  * If this leads to evictions due to overuse of the cache set, then this can lead to cache misses which are called conflict misses
  * If variables which are used together are also stored together, the likelihood of them falling into the same set is minimized
* Experiment that iterates over circular list elements
  * If few elements are used, all the data fits into the L1d and the access time is 3 cycles per list element
  * The access time also holds for almost all arrangmenets of list elements - the virtual addresses are nicely mapped to L1d slots with almost no conflicts
  * If the distance between two list elements is a multiple of 4kb (i.e. 64 empty elements) and the list length is greater than 8, then the average number of cycles increases to 10 cycles per list element
  * This is because all entries are in the same set, and once the list length is greater than the associativity, entries are flushed from the L1d and are then re-read from the L2 cache in the next round
  * Can determine the processor used has an L1d cache with an associativity of 8 and a total size of 32kb

