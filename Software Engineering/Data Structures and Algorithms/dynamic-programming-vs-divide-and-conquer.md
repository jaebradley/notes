# [Dynamic Programming vs. Divide And Conquer](https://itnext.io/dynamic-programming-vs-divide-and-conquer-2fea680becbe)

* Two key attributes that divide and conquer must have in order for dynamic programming to apply
  * Optimal solution can be constructed from optimal solutions of its subproblems
  * Problems can be broken down into subproblems which are reused several times or a recursive algorithm for the problem solves the same subproblem over and over rather than always generating new subproblems

  ## Divide and Conquer Example: Binary Search

  * Iteratively breaking the original array into sub-arrays and trying to find required element in each sub-array
  * No overlapping subproblems so cannot use dynamic programming - each time array is split into completely indepedent parts
  * Drawing decision tree for algorithm implies that it's divide and conquer (note: a decision tree and not a decision graph)

  ## Dynamic Programming Example: Minimum Edit Distance

  * In order to transform `ME` to `MY` need to know three values
    * minimum edit distance for `ME` to `M`
    * minimum edit distance for `MY` to `M`
    * minimum edit distance for `M` to `M`
  * Find minimum of these three values and then add `1`
  * The solution is recursive since it relies on calculating the minimum edit distance of three previous values

```bash
__|   | M | E 
  | 0 | 1 | 2
M | 1 | 0 | 1
Y | 2 | 1 | 1
```

* All formula needs is three adjacent cells to calculate minimum edit distance for a given cell
  * Find minimum of three adjacent cells `(i - 1, j)`, `(i - 1, j - 1)`, and `(i, j - 1)` and then add `1` if there are different letters in `i` row and `j` column
* This uses overlapping subproblems
  * One example is that `M` to `ME` requires knowing minimum edit distace of `M` to `M` as does `ME` to `M`
* Since matrix calculation depends on previous value, the tabulation technique of filling cache from bottom-up direction is used