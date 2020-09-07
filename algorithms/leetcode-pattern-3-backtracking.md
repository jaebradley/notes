# [Leetcode Pattern 3: Backtracking](https://medium.com/leetcode-patterns/leetcode-pattern-3-backtracking-5d9e5a03dc26)

* If need to return a collection of all the answers, that would require backtracking
* Same with what the actual solutions are vs the optimum value of some parameter (which would be more likely DP)
* Collect all the possible subsets of a set (so `[1, 2, 3]` is `[1]`, `[1, 2]`, `[2. 3]` etc)
* Use a recursive function to loop through the remaining numbers
  * For each number, add it to the current subset that's being evaluated
  * For the remaining numbers, call the current function recursively for the current subset (so that it evaluates all possible subsets that start with the current subset)
  * After all subsets have been evaluated, remove the current value from the current subset, and explore next subsets that don't include current value
* N Queens starts by placing queens column by column
  * So place a queen in a column and try to place another queen in the next column so that it's valid
  * If a queen position is invalid, try other rows in the same column
