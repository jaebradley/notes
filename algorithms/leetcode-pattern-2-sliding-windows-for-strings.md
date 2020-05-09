# [Leetcode Pattern 2: Sliding Windows For Strings](https://medium.com/leetcode-patterns/leetcode-pattern-2-sliding-windows-for-strings-e19af105316b)

* Simple sliding window problem that sets example is "given an array of n integers, calculate maximum sum possible for k consecutive elements in an array"
  * Brute force would be to double `for` loop 
  * However, notice that having computed first window of first `k` elements, to get the next window leave out the leftmost value (or first item) and add the rightmost item to get the new sum
* Minimum window substring that has all characters of input (`T`) uses two-pointers for the beginning and end of the window
  * Keep track if a substring has all the characters in `T` - build a frequency table which keeps track of the count of characters in the current substring relative to their count in `T`
  * Keep sliding to the right, examining each character and updating the count in the frequency table
  * Keep track of the count of matching characters in `T` using frequency table - when this count hits `0`, attempt to trim it down by removing unessential characters from the left side
  * Continue trimming until answer is invalid - then start adding characters from the right
* This pattern of keeping a frequency of characters can be applied to other problems, like finding all anagrams in a string (where the "matching" condition is not if the character count matches, but if the character count matches _and_ the length of the substring matches the length of the anagram string)