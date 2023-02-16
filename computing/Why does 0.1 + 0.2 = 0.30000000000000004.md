# Why does 0.1 + 0.2 = 0.30000000000000004?

* How floating point addition (roughly) works
  * Add numbers together with additional precision
  * Round the results to the nearest floating point number
* Exact value of `0.1` as a 64-bit float is `0.10000000000000000555111512312578270211815834045410156250000000000000000000000000`
* Exact value of `0.1 + 0.2` (as a 64-bit float` is `0.3000000000000000166533453693773481063544750213623046875`
* Floating point numbers around `0.3`
  * `"0.3"` => `0.29999999999999998889776975374843459576368331909179687500000000000000000000000000`
  * Next floating point number after `0.3` is `0.30000000000000004440892098500626161694526672363281250000000000000000000000000000`
* Result of `0.1 + 0.2` is exactly between the two floats, so round to the floating point number with the even significand

## Binary Calculation

* `floating point number = sign x 2^exponent x (1 + (significand / 2^52))`
* `1 x 2^1 x (1 + (2^51/2^52))` -> `2 x (1 + 1/2)` -> `3`
