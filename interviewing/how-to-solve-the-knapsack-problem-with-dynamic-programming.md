# [How To Solve The Knapsack Problem With Dynamic Programming](https://medium.com/@fabianterh/how-to-solve-the-knapsack-problem-with-dynamic-programming-eb88c706d3cf)

* 0-1 knapsack problem can be described using the following situation

> Consider a thief gets into a home to rob and he carries a knapsack. There are fixed number of items in the home — each with its own weight and value — Jewelry, with less weight and highest value vs tables, with less value but a lot heavy. To add fuel to the fire, the thief has an old knapsack which has limited capacity. Obviously, he can’t split the table into half or jewelry into 3/4ths. He either takes it or leaves it.

* In the example problem, suppose we have a knapsack that can hold up to a weight of `10`
* Suppose there are four total items to choose from, with the values of `10`, `40`, `30`, and `50`
  * And also with weights `5`, `4`, `6`, and `3`


## Solution

* Create a 2-dimensional array where there are rows for each itemand columns for each weight from `0` to `10`
* The row number represents the set of all items from row 1 to the current row number
  * The values in row `3` assumes items `1-3` are included
* The column number represents the weight capacity of the knapsack
  * The values in column 5 assumes the knapsack can hold 5 weight units
* An entry in row `i`, column `j` represents the maximum value that can be obtained with items `1-i` that can hold `j` weight units
* Can fill base cases in table, like row `0` (when no items can be picked) has column values of `0`
* Similarly, for column `0` (when knapsack must be empty) the maximum value must be `0`
* For a given row `i` / column `j`, we can either include item `i` or not
* So need to compare the maximum value that can be obtained with items `1-i` vs. maximum value if we do not include item `i`
  * Maximum that can be obtained if item `i` is not included is the value in the previous row for the same column
* If item `i` weighs more than the remaining capacity in the knapsack, it can't be included
