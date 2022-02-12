# [How does UTF-8 turn “😂” into “F09F9882”](https://sethmlarson.dev/blog/utf-8)

* UTF-8 is an encoding that describes encoding Unicode characters into bytes
* Unicode uses a concept of a codepoint, which is essentially a number, that maps to a Unicode character in the Unicode standard

## The 5 octets of UTF-8

* Every octet that is produced by UTF-8 is either a header octet or a tail octet
* The header octets specifies the length of the encoded bytes
  * `0xxxxxxx` represents a 1 octet header. `110xxxxx` represents a 2 octet header, `1110xxxx` represents a 3 octet header, `11110xxx` represents a 4 octet header
  * Specific unicode code point ranges have specific header octets
* In constrast, a tail octet has the following format `10xxxxxx`
* The `x`s are just bits that are filled with data
* The combination of header + tail octets means that UTF-8 is a variable-length encoding, meaning it can be one byte to 4 bytes in length

## Following the flow of bits

* 😂 has a codepoint of `0x1F602`
* The codepoint is in the range for the 4 octets header
* `0x1F602` is `11111011000000010`
* Since we need to fill 4 octets, we basically need to fill the following `x` "data" bits: `11110xxx 10xxxxxx 10xxxxxx 10xxxxxx`
  * In all, there should be 21 "data" bits
* In the previous hex to bit conversion, there were 17 bits, so need to pad with zeroes until there are 21 bits
  * `000 0111111 011000 000010` (note how each separate part aligns bit-wise with the `x`s above)
* Decoding a Unicode codepoint from bytes requires reversing the process
  * Each byte is examined for the header type and then individual bits are extracted from each octet and added together to reproduce the codepoint

