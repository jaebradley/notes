# [XOR - The Magical Bitwise Operator](https://medium.com/hackernoon/xor-the-magical-bit-wise-operator-24d3012ed821)

* If both bits in the compared position of the bit patterns are 0 or 1, the bit in the resulting bit pattern is `0`, otherwise `1`
  * It returns `1` only if exactly one bit is set to `1`
* Return rightmost `1` in binary representation of number
  * `1010` -> `0010`
  * Binary subtraction property - `1000 - 0001` -> `0111`
    * The property is, the difference between the binary number `n` and `n-1` is all the bits on the right of the rightmost `1` are flipped including the rightmost `1`
    * In other words, after subtracting `1`, the first `0` seen when reading right-to-left is the rightmost `1`
  * Using binary subtraction property, to get rightmost `1`, `x ^ (x & (x - 1))`
* For a given array, every element is repeated twice except one. Return the non-repeated element.
  * `n ^ n = 0`
  * `n ^ 0 = n`
  * Iterate over array, and for each value, take the current value (start with `0`) and `XOR` the current value with the current array iteration value
    * For elements that are repeated, the `XOR`s will "cancel" out
    * For the element that is not repeated, since there is no second `XOR` to cancel it out, it will be the remaining value after iterating through the array
