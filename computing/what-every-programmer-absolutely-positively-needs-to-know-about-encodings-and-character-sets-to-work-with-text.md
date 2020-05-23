# [What every programmer absolutely, positively needs to know about encodings and character sets to work with text](https://kunststube.net/encoding/)

* To convert a sequence of bits into letters, an encoding scheme needs to exist
* `01100010` => `b` in the `ASCII` encoding scheme

## `ASCII`

* Consists of eight bits each (i.e. a byte)
* Consists of 95 human-readable characters
  * `a-z` and `A-Z`
  * `0-9`
  * Punctuation marks and other characters
  * Whitespace characters
  * All-together, 128 characters (uses all possible combinations of 7 bits)
  * Unfortunately, non-English characters open up even more necessary characters, even if using the extra bit (`ASCII` only used 7 out of the 8 bits in the byte)

* Can write numbers in binary or other value dependening on number system (`10011111` in binary is `9F` in hex) - often times character codes are represented in hex since it's easer to read

## Multi-byte Encodings

* 2 bytes (16 bits) makes it possible to encode 65,536 distinct values
* Unicode is an encoding standard to unify all encoding standards
* 1,114,112 code points to encode every possible character known to mankind
  * It even has unofficial section for Klingon
* Unicode is _not_ an encoding though - it just specifies what certain code points stand for ("65 stands for A")
* How these code points are actually encoded into bits is different than Unicode
* Three bytes are enough to encode all the Unicode code points but three bytes is awkward to work with so often times four bytes are used
* However, unless you're using Chinese or some other characters that take a lot of bits to encode, you're never going to be using a large amount of the total number of bytes
* `UTF-32`, `UTF-16`, and `UTF-8` are all encodings that encode Unicode code points into bits
  * The latter two encodings are variable-length encodings which means that if a character can be represented as a single byte (small code point) then it will be encoded with a single byte
  * `UTF-8` has ways of using the hightest bits in a byte to signal how many bytes a character has

* Primary cause of garbled text is that somebody is trying to read a byte sequence using the wrong encoding - computer always needs to be told what encoding some text is in
* Sometimes a program will insert a "Unicode replacement character" if it cannot correctly decode a character and is trying to handle Unicode
  * No way to reverse-engineer original characters when they are replaced
* Scenario involving encodings that are handled incorrectly but things still work
  * Database that's set to `latin-1` and app that works with `UTF-8`
  * Almost every combination of `1`s and `0`s is valid in single-byte `latin-1` encoding
  * So `11100111 10111000 10100111` gets stored in database with the value in the DB being three `latin` characters but then in the app it gets rendered as the `UTF-8` sequence for some other character
* `ASCII` maps 1 to 1 with `UTF-8` 
  * All `ASCII` characters take up 1 byte in `UTF-8` and they're the exact same bits that are used in `ASCII`
  * Any character _not_ in `ASCII` takes up two or more bytes in `UTF-8`
