# 9.9.5 Program Structure

* Example using C program with a 128 by 128 array of integers
* The array is populated by iterating over all the columns first, then iterating over the rows

```c
for (j = 0; j < 128; j++) {
  for (i = 0; i < 128; i++) {
    data[i][j] = 0;
  }
}
```

* For pages of 128 words, each row takes one page
* So this program will read a page, and then zero a word in the page, then move on to the next page
* If the operating system allocates fewer than 128 frames to the program, then its execution will result in 128 x 128 (16.4k) page faults
* If the `for` loops are flipped, there will instead be 128 page faults (one page fault for each new page that is read)
* Stack data structure has good locality (since the top of the stack is accessed)
  * In contrast, a hash table has poor locality as references are scattered
* Pointer-based languages like C or C++ use pointers frequently and pointers tend to randomize access to memory, diminishing a process's locality
