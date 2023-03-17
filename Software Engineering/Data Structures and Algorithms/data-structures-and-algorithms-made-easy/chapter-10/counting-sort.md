# Counting Sort

* For an input element X, how many elements are less than X
* Only works for integers
* Only works when defined range of integers is known
* Algorithm
  * Scan values and keep track of count for specific values (number of `1`s, `2`s, etc.)
  * Scan counts, and cumulatively sum values from previous indices (so number of `3`s is number of `1`s + number of `2`s)
  * These cumulative counts should indicate where the first instance of the value at that index should be located
  * Scan through original values
    * For each value, lookup index in cumulative counts
    * Add value in sorted array at that index
    * Increment index value in cumulative counts - this is so that the next instance of that value is at the next index
* Runtime
  * `O(n + k)` where `n` is size of array and `k` is size of range
    * When `n` and `k` differ greatly, generally this sorting strategy is not used (i.e. if range is in the millions but there are only five values it doesn't make sense to use this strategy)
    * So, since it's used when `n` and `k` are generally close, this sorting strategy is considered `O(n)`
