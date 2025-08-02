# [Extending that XOR Trick to Billions of Rows - an Introduction to Invertible Bloom Filters](https://nochlin.com/blog/extending-that-xor-trick)

## Finding 3 Missing Numbers
```
A = [1,2,3,4,5,6,7,8,9,10]
B = [1,2,4,5,7,8,10]
```
* Set `B` is missing `{3, 6, 9}`
* XOR trick works for finding 1 or 2 missing numbers
* But if there are 3+ missing numbers, need to partition sets using a hash function
* In the example, three partitions are created for both `A` and `B`
```
// Hash function assigns each number to a partition
H(1) = 2, H(2) = 1, H(3) = 1, H(4) = 0, H(5) = 0
H(6) = 0, H(7) = 2, H(8) = 1, H(9) = 2, H(10) = 1

// Partitioned sets
A_0 = [4,5,6]     B_0 = [4,5]
A_1 = [2,3,8,10]  B_1 = [2,8,10]
A_2 = [1,7,9]     B_2 = [1,7]
```
* Each partition has, at most, 1 missing element
* Can then apply the XOR trick to each partitioned set

## Detecting when the XOR trick fails
* Comparing counts doesn't work, because what we really want is the symmetric difference between sets
  * What elements are in either set, but not _both_ sets
* Hash each element in Set `A` and then `XOR` these elements
* Do the same for each element in Set `B`
* `XOR` these two "hashed + `XOR`ed" values
* If the result is not the hash of `0` then we can't simply use the naive count-based approach to detect the missing numbers

## Invertible Bloom Filters
<img width="807" height="1184" alt="image" src="https://github.com/user-attachments/assets/12f91483-57ee-47fd-b1f7-bb2d63eed3bc" />

* IBF is an array of cells
* Each cell contains three values
 * `XOR` of all the element values
 * `XOR` of all the hashed element values
 * Count of all the elements in the cell
* IBFs have one tuneable parameter - the expected size of the symmetric difference
* From [this HackerNews discussion thread](https://news.ycombinator.com/item?id=44561895):
>The graph constructed by using bloom filter-style hash functions supports a decoding process called "peeling" where you:
>1. Find a batch with 1 missing element
>2. Delete that element from its other assigned partitions
>3. Repeat, as the modified batches may now be recoverable.
>This iterative process (surprisingly!) succeeds with very high probability as long as the number of partitions is 1.22x larger than the number of missing elements with k=3 hash functions.
