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
