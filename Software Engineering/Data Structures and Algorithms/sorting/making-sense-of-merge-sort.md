# [Making Sense of Merge Sort](https://medium.com/basecs/making-sense-of-merge-sort-part-2-be8706453209)

* Divide-and-conquer algorithm (divides problem set in half and applies sorting logic on each subset / subproblem - i.e recursion)
* Out-of-place algorithm that requires additional memory (biggest drawback)
* Implementation
  * Split array of values into two parts
  * On each part, split each part into two parts,
  * Keep doing this until arrays are size 1
  * Merge arrays back together in sorted order
    * This is done by taking two arrays at a time
    * Iterating through each element in each array
    * Comparing the first element in each array against the other and taking the resultant element
    * Adding element into third array that represents merged elements
  * Do this until single merged and sorted array
* Runtime is `O(n log n)`
  * This is because the number of append operations is `n log n`
  * Append operation is in merge step where value is added to third array that represents merged elements
