# 2.3 Mathematical Equivalence of Binary and Decimal

* `123` is the same as `1 x 100 + 2 x 10 + 3 x 1`
* The right most digit (`3` in the case of `123`) is called the **least significant digit** (because it counts the lesat in the total value of the number)
* The left-most digit (`1) is called the **most significant digit** (because it counts the most in the total value of the number)
* Any number can be broken down the the following notation
  * A digit at position `i` is equal to the base raised to the power of `i - 1`
  * So in the case of `123`, the `3` is really `3 x 10^0` which is `3 x 1`
* The formula to convert a binary to unsigned decimal is to iterate over all the digits, and to calculate the the digit multiplied by `2` to the "place" of the digit, and to take the result of that calculation and add it to the same sum

```python
result = 0
for i in range(0, number of digits):
  result = result + digits[i] * ( 2 ** i)
```
