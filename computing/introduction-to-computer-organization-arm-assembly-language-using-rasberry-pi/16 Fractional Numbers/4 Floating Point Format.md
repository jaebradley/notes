  # 16.4 Floating Point Format
  
  * Think of values written in scientific format so `1,024` => `1.024 x 10^3`
    * `1.024` is the significand and the `10^3` is the exponent
  * Note that the significand value is such that only one digit appears to the left of the decimal point
  * Also need the sign of the significand as well
  * Floating point format can represent a larger range of values
 
## Example using four-digit unsigned decimal system where the range of integers are 0 to 9999 inclusive

* Can represent `3.9 x 10^-4` as `3|9|1|4` or `3914` where `1` represents the exponent sign
* Can allow numbers in range `1.0 x 10^-9` to `9.9 x 10^9` inclusive
  * So `0119` to `9909`
  * Note that while the range of possible values has increased, the same number of possible values (`1,000`)
  * Sacrifice additional range for less accuracy
  * `9111` represents `9.1 x 10^-1` and `9311` is represents `9.3 x 10^-1`
    * The sum is `1.84`, which is represented as `1.8` in our system (`1800`)
  * `9411` represents `9.4 x 10^-1` and the sum with `9.3 x 10^01` is `1.87`
    * After rounding, get `1.9 x 10^0`
  * Start off with values ecpressed to nearest 1/100th and the sum is accurate only to nearest 1/10th
* To compare with fixed point arithmetic, fixed point could represent `0.93` using four digits by having the first two digits represent the "integer part" and the last two digits represent the "fractional part"
  * Storage scheme allows the storing the result of both additions, exactly
