# [The Absolute Essentials For Bit Manipulation in JavaScript](https://lucasfcosta.com/2018/12/25/bitwise-operations.html)

* Base 10 uses 0-9 to represent a single place, once reach 10 for a place, set place to zero and set next place to 1
  * 22 is just 10 units repeated 2 times plus 2 units
* Byte is a group of 8 bits
* To support postive and negative numbers set left-most bit to 1 for negative numbers and 0 for positive numbers
* In JavaScript
  * Hexadecimals start with `0x`
  * Octals start with `0o`
  * Binaries start with `0b`
* When using bitwise operators, all operands get converted to signed 32-bit integers
  * 124 turns into `00000000 00000000 00000000 01111100` instead of just `01111100`
* Bitwise `AND` (`&`) returns `1` if both digits are `1`
  * `0b10101` & `0b10011` will result in `0b10001`
  * `0b10101` is 21, `0b10011` is 19 and `0b10001` is 17
* Bitwise `OR` (`|`) returns `1` if at least one of the digits is `1`
  * `0b10101` | `0b10011` will result in `0b10111` (23)
* Bitwise `XOR` (`^`) return 1 whenever the corresponding bits are different
* Bitwise `NOT` (`~`) inverts an operand - 1 turns into 0 and vice-versa
* Bitwise left shift (`<<`) shifts bits to the left by the number of bits specified by the right value
  * `0b010101` << 1 will result in `0b101010`
  * Each new digit on the right will be replaced with a 0
  * Since JavaScript has 32-bit integers, left shifting will swap the left-most bit which holds sign information
* Bitwise right shift (`>>`) shift bits to the right by the number of bits specified by the right value
  * The bit values added to the left will be the same as the leading bit value on the left
  * So if the leading left bit value is 1 then the bit values added to the left when right-shifting will be 1
* Bitwise right shift zero shift (`>>>`) will shift bits to the right and replace bits with 0 regardless of what the left-most bit value is
* Addition is just carrying next digit when sum of bits is greater than 1 (when talking about binary addition)
* Subtraction is addition between first bit and inverse of second bit
  * Inverting a bit involves using two's complement
  * two's complement involves inverting all bits, and adding 1
  * `00000011` (3) => `11111100` => `11111101` (-3)
  * `00000111` (7) + `11111101` (-3) => `00000100` (4)
  * Note that overflow is discarded

## Bitmasking

* Getting a specific bit
  * `10110101` and you wanted third bit from right to left (i.e. bit at index 2)
  * AND `00000100` with the previous number
  * AND will change all bits except the third one (if it was 0 it will stay 0 and if it was 1 it will remain 1)
  * Right-shifting result by 2 will place bit value as first bit
* Setting a specific bit to one
  * If for previous number want to set fourth bit to 1 generate `00001000` and OR it with previous number
  * All other bits will stay the same but the fourth bit will be a 1
* Setting a specific bit to zero
  * Shift bit to specific position in bitmask (like `00001000`)
  * NOT the bitmask so that it looks something like `11110111`
  * AND the new bitmask so that all bit values stay the same except for the value at the 0 - this will be 0 if it isn't already
 
