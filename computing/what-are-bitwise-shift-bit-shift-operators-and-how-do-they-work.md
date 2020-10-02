# [What Are Bitwise Shift Bit Shift Operators And How Do They Work](https://stackoverflow.com/a/62363693/5225575)

* This information is Java-specific
* Left shift (`<<`) shifts bits to the left
  * Integers are stored in memory as a series of bits - the number `6` is stored as a `32`-bit `int` like `00000000 00000000 00000000 00000110`
  * Shifting by one position to the left (`6 << 1`) would result in the number `12` (`00000000 00000000 00000000 00001100`)
  * Shifting by one position to the left is equivalent to multiplying by `2`
  * Shifting `11100000 00000000 000000000 000000000` by one to the left would lead to `11000000 00000000 000000000` as the bits do not "wrap"
* Logical right shift (`>>>`) will move bits to the right
  * Differs from arithmetic right shift (`>>`) in that arithmetic right shift pads with the most significant bit meaning that arithmetic right shift preserves the sign of the value
  * Shifting `100000000 00000000 00000000 01100000` by logical right shift (`>>> 4`) would lead to `00001000 00000000 00000000 00000110`
  * Shifting the same value using arithmetic right shift (`>> 4`) would lead to `11111000 00000000 00000000 00000110` - note how the leading `1` is still preserved (i.e. the sign bit)
