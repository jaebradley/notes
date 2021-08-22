# 2.5 Unsigned Decimal to Binary Conversion

* To convert an unsigned decimal to binary
* For each digit (from least-significant to most-significant i.e. right-to-left)
  * calculate the modulo of the digit with respect to `2`
  * calculate the digit, divided by two (without a remainder)
  * at this point, the result of the modulo calculation represents the remainder and the result of the division by `2` represents the quotient of the digit
  * the remainder represents the binary digit
  * continue doing this process with the calculated quotient until the quotient is `0`
* So `123`
  * Divided by `2` is `61`, modulo is `1`, so first binary digit is `1`
  * `61 / 2` is `30`, modulo is `1`, so second binary digit is `1`
  * `30 / 2` is `15`, modulo is `0`, so third binary digit is `0`
  * `15 / 2` is `7`, modulo is `1`, so fourth binary digit is `1`
  * `7 / 2` is `3`, modulo is `1`, so fifth binary digit is `1`
  * `3 / 2` is `1`, modulo is `1`, so sixth binary digit is `1`
  * `1 / 2` is `0`, modulo is `1`, so seventh binary digit is `1`
  * Since quotient is now `0`, stop calculation
  * Resultant binary representation is `1111011`
