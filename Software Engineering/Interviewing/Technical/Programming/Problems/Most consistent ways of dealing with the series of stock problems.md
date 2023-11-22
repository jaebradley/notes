# [Most consistent ways of dealing with the series of stock problems](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/solutions/108870/most-consistent-ways-of-dealing-with-the-series-of-stock-problems/)

## Main Idea

* `T[i][k]` is the maximum profit gained at the end of the `i`th day with at most `k` transactions
* Note that `T[-1][k]` and `T[0][k]` will both be `0`
  * On the `-1`th day and first `0`th / first day, there are no stock transactions so max profit is `0`
* Actions on any given day are to buy, to "rest" or to sell
  * Try each option and choose the one that maximizes profit
  * Some problems have an extra restriction where multiple transactions are disallowed
      * There must be exactly `0` stock in hand when buying
      * Exactly `1` stock in hand when selling
* `T[i][k][0]` and `T[i][k][1]`
  * `T[i][k][0]` = max profits at end of day `i` with (at most) `k` transactions and `0` stock in hand AFTER action (buy, sell, rest)
  * `T[i][k][1]` = max profits at end of day `i` with (at most ) `k` transactions and `1` stock in hand AFTER action (buy, rest)
* Recurrence relations
  * `T[i][k][0] = max(T[i-1][k][0], T[i-1][k-1][1] + prices[i])`
    * Max of resting from previous period, or selling + max profit from previous period with 1 stock in hand
  * `T[i][k][1] = max(T[i-1][k][1], T[i-1][k-1][0] - prices[i])`
    * Max of resting from previous period (where 1 stock in hand) or buying + max profit from previous period with 0 stock in hand
  * Max profit will be `T[i][k][0]` (max profit will always be when ending up with `0` stock in hand)

## Application

### When `k = 1`

* Two unknown variables - `T[i][1][0]` and `T[i][1][1]`
* Recurrence relations
  * `T[i][1][0] = max(T[i-1][1][0], T[i-1][1][1] + prices[i])`
  * `T[i][1][1] = max(T[i-1][1][1], T[i-1][0][0] - prices[i])`
    * Note that `T[i][0][0]` is always `0` (cost of `0` actions to end with `0` stock in hand)
    * Simplifies to `T[i][1][1] = max(T[i-1][1][1], -prices[i])`

### When `k = Infinity`

* No big difference betwen `k` actions and `k-1` actions
  * `T[i-1][k-1][0] = T[i-1][k][0]` and `T[i-1][k-1][1] = T[i-1][k][1]`
* Recurrence relations
  * `T[i][k][0] = max(T[i-1][k][0], T[i-1][k][1] + prices[i])`
  * `T[i][k][1] = max(T[i-1][k][1], T[i-1][k-1][0] - prices[i])`
    * Simplifies to `max(T[i-1][k][1], T[i-1][k][0] - prices[i])` since `T[i-1][k-1][0] = T[i-1][k][0]`

```java
public int maxProfit(int[] prices) {
    int T_ik0 = 0, T_ik1 = Integer.MIN_VALUE;
    
    for (int price : prices) {
        int T_ik0_old = T_ik0;
        T_ik0 = Math.max(T_ik0, T_ik1 + price);
        T_ik1 = Math.max(T_ik1, T_ik0_old - price);
    }
    
    return T_ik0;
}
```

* Logic suggests a greedy strategy
  * Buy at each local minimum
  * Sell at the next local maximum
  * Equivalent to finding all the increasing subarrays in `prices` and buying at the starting price of each subarray and selling at its end price


### `k = Infinity` with cooldown

* Cooldown after buying implies that for buying on day `i`, need to look two days behind instead of looking at the previous day
* Recurrence relations
  * `T[i][k][0] = max(T[i-1][k][0], T[i-1][k-1][1] + prices[i])`
  * `T[i][k][1] = max(T[i-1][k][1], T[i-2][k-1][0] - prices[i])`
  * Since `T[i][k-1][0/1]` equals `T[i][k][0/1]` can replace these in recurrence relations

### `k = Infinity` with transaction fee`

* Only adjustment to recurrence relations is that there is now a fee associated with buying (or selling)
* Selling incorporates the fee like `T[i][k][0] = max(T[i-1][k][0], T[i-1][k-1][1] + prices[i] - fee)`
* Buying incorprosates the ee like `T[i][k][1] = max(T[i-1][k][1], T[i-1][k-1][0] - prices[i] - fee)`
* Note that when `k = Infinity` all the `T[i-1][k-1][0/1]` values are the same as `T[i-1][k][0/1]`
