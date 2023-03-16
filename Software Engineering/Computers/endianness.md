# [Endianness](https://developer.mozilla.org/en-US/docs/Glossary/Endianness)

* Every byte can store an 8-bit number, so must reserve more than one byte if storing a larger number
* In such a case, the ordering of these bytes can be such that the bytes are stored from least-to-most significant (i.e. the least significant byte takes the smallest address)
  * Similar to "European" way of formatting dates with the day of month first, then month, then year
  * This is little endian, which is used on all Intel processors
* The opposite, where the most-significant byte is stored at the smallest address, is called big endian
  * Similar to ISO date (Y-M-D)
  * Often this is called "network byte order" because Internet standards usually require data to be stored big-endian
* As a visual aid, the hex value `0x12345678` (or `305,419,896`)
  * Has the following representation in little-endian `0x78` `0x56` `0x34` `0x12` - the least significant bytes are first
  * Has the following representation in big-endian `0x12` `0x34` `0x56` `0x78` - the most significant bytes are first
