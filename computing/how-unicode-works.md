# [How Unicode Works](https://deliciousbrains.com/how-unicode-works/)

* ASCII was invented as basically a lookup table between bytes and characters (characters represented as 8 bits / 1 byte)
  * ASCII table has 128 characters (a-z, A-Z, 0-9)
* Unicode is still a lookup of bits to characters
  * Unicode encoding schemes are efficient
  * In UTF-8 if a character can be represented with 1 byte that's all it will use
  * If a character uses 4 bytes it'll get 4 bytes
  * Called variable length encoding
  * UTF-8 has the same 128 code points as ASCII
* Code points are atomic units of information
  * Text is a sequence of code points
  * Each code point is a number that means something in the Unicode standard
* Current Unicode standard defines ~1.1 million code points
* 💩 -> `U+1F4A9` -> `0001 1111 0100 1010 1001`
* JavaScript engines use UTF-16 internally
  * UTF-16 is like UTF-8 except the lowest amount of fewest used is 16
  * Strings are treated as UTF-16 code units which means that two code points might be used to reference a character
  * `'💩'.length` -> 2 because JS uses UTF-16 encoding and `length` returns the UTF-16 code unit length of a given character so complex characters can have a length of 2
  * `String.prototype.normalize` converts strings to standardized Unicode format
* MySQL's UTF-8 isn't really UTF-8 since `utf8` encoding only uses 3 bytes
  * Instead use the `utf8mb4` encoding which allows up to four bytes per character
