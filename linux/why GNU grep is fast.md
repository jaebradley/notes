# [why GNU grep is fast](https://lists.freebsd.org/pipermail/freebsd-current/2010-August/019310.html)

* It avoids looking at every input byte
* Executes very few instructions for every byte that it does look at
* Uses Boyer-Moore algorithm, which looks at the final letter of the target string first, and uses a lookup table to identify how far ahead it can skip in the input whenever it finds a non-matching character
* GNU grep uses raw Unix input system calls and avoids copying data after reading it
* GNU grep avoids breaking input into lines as looking for new lines would slow grep down by a factor of several times as finding new lines requires looking at every byte
* Instead of using lines, GNU grep reads raw data into a large buffer, searches the buffer using Boyer-Moore, and only when it finds a match does it go and look for the bounding new lines
* GNU grep tried to avoid having the kernel handling every byte of input (so using `mmap` vs. `read` for file input)
  * Using `read` caused most Unix versions to do extra copying, and while `mmap` is now non-standard, can still force GNU grep to use `mmap`, by using the `--mmap` option
  * Where the data is already in the file system buffer caches, `mmap` is faster than `read`
* Try to set things up with things like page-aligned buffers, page-sized read chunks where the kernel can also avoid copying bytes
