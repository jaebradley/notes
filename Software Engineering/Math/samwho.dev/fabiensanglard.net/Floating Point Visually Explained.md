# [FLOATING POINT VISUALLY EXPLAINED](https://fabiensanglard.net/floating_point_visually_explained/)
* In C, floats are 32-bits (IEEE 754 standard)
  * Allow operations on approximation of real numbers
* 32-bits are divided into three sections
  * 1 bit for the sign
  * 8 bits for the exponent
  * 23 bits for the mantissa
 
<img width="1880" height="390" alt="Screenshot 2025-09-13 at 09 26 03@2x" src="https://github.com/user-attachments/assets/66f999ed-4753-42c3-8ccc-fb8294c7ec1f" />

* Floating point formula = `(-1 ^ sign bit) * 1.mantissa * (2 ^ (exponent - 127))`
* Author's mental model of exponent and mantissa is
  * Exponent = window between two consecutive powers of two integers
  * Mantissa = offset within that window
* Window describes within which two consecutive power-of-twos that the number exists in
  * For example: `[0.5, 1], [1, 2], [2, 4], [4, 8], etc`
* The offset divides the window into `2 ^ 23` buckets

<img width="1506" height="544" alt="Screenshot 2025-09-13 at 09 33 44@2x" src="https://github.com/user-attachments/assets/e8f9b5a7-78c9-42b7-b358-b6b884ddcc7b" />

* For example, `6.1` would be encoded within the `[4, 8]` window
 * The offset would be one of the "buckets" within the window

## Precision
* More precision for smaller windows (i.e. the `2 ^ 23` buckets are spread across a smaller range)
 * For example, in the window `[1, 2]`, each bucket has a precision of `0.00000011920929`
 * For the window `[2048, 4096]`, each bucket has a precision of `0.0002`

## `3.14` Example
* Sign bit is `0` since `3.14` is positive
* `3.14` is between `[2, 4]`
 * From the previous "formula" the exponent is `2 ^ (exponent - 127)` so in this case, the exponent is `128`
* The offset is calculated by evaluating `(3.14 - 2)/(4 - 2)` (`0.57`)
 * Mantissa = `2 ^ 23 * 0.57` (`4781507`) = `10010001111010111000011b`
