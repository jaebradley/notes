# [Counting Linearly with Counting Sort](https://medium.com/basecs/counting-linearly-with-counting-sort-cd8516ae09b3)

* Can only be implemented on integers
* Need to know range of input integers

## Example

* Assume values are `[9, 4, 1, 7, 9, 1, 2, 0]`
* Let's say we also know that all data is between `0` and `9`
* Build `count` array which will store the counts how many of each number there are in our input array in indices `0` to `9`
* For our example, the `count` array will look something like `[1, 2, 1, 0, 1, 0, 0, 1, 0, 2]`
  * Index represents values
* Scan over counts array, and replace value at each index with cumulative sum of all previous indices
  * `[1, 3, 4, 4, 5, 5, 5, 6, 6, 8]`
  * These values represent the number of values that are the less than or equal to the index
  * However, what we really want is the number of values that are strictly less than the index
  * We can get this by simply moving the cumulative counts over one index
  * `[0, 1, 3, 4, 4, 5, 5, 5, 6, 6]` - now each value represents the values that are less than the index
* Scan values, find respective counts, and insert value at that index
  * Using the cumulative counts array, find the respective count for a specific value
  * Insert the value at the index determined by the respective count value
  * Increment the value in the cumulative counts array (if we see a next instance of the same number, it should be inserted at the index directly to the right of the previous value)
  * In our example
    * So `9` is the first value
    * Lookup counts - `6`
    * Insert `9` at index `6`
    * Update cumulative counts for index `9` to be value `7`

## Characteristics

* `O(n + k)` where `n` is number of values and `k` is range between largest and smallest element
* Counting sort isn't used if `k` is much greater than `n`
  * Thus, when `n` and `k` are similar, the runtime is closer to `O(n)`
* Stable sort
* Space complexity is `O(k)` (need that counts array)
