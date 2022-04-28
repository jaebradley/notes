# 16.1 Fractional Values in Binary

* Can think of decimal in fractional powers of 2 like `digit right of decimal x 2^-1` + `digit 2 places right of decimal x 2^-2` + etc.
* So `123.6875` is equivalent to `1111011.1011` in binary
  * `1 x 0.5` + `0 x 0.25` + `1 x 0.125` + `1 x 0.0625` => `0.5 + 0.125 + 0.0625` => `0.6875`
* Any exact representation of fractional values in binary is limited to sums of inverse powers of two
* Rounding fractional values in binary works by looking at the bit to the right of the position to round off
  * If the bit to the right is 1, then add one to the bit position where rounding off
  * If the bit to the right is 0, no need to do anything
  * Rounding `0.9` (`0.1110011001100...`) to the nearest 8th binary position would be `0.11100110` since the next bit is `0`
  * Rounding error would be `0.9 - 0.11100110 base 2` => `0.9 - 0.8984575` => `0.0015625`
