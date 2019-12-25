# [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)

## ASCII and ANSI

* ASCII able to represent every (unaccented) English letter using number between 32 and 127 (a space was 32 and A was 65)
  * Could be stored using 7 bits
  * Most computers used 8-bit bytes so people used the extra bit for many things like providing accented characters for European languages or horizontal / vertical bars
* ANSI standard - basically ASCII below 128 and then above 128 relied on code pages
  * Israel DOS used code page 862 while Greek used 727
  * Asian alphabets have thousands of letters, so new system called double-byte character set was created
    * Some letters stored in one byte and others were stored using two bytes
    * Easy to move foward in a string but more difficult to move backwards

## Unicode

* A letter maps to a code point - A is `U+0041`
  * The numbers are hexadecimal
  * No real limit to number of letters that Unicode can define - more than 65,536 code points so not every unicode letter can be squeezed into two bytes
* `Hello` -> `U+0048 U+0065 U+006C U+006C U+006F`
* UTF-8 is a system (encoding) for storing Unicode code points in memory using 8-bit bytes
  * Every code point from 0-127 is stored in a single byte
  * Code points 128+ are stored using up to 6 bytes
  * English text looks exactly the same in UTF-8 as it does in ASCII
  * `Hello` would be stored as `48 65 6C 6C 6F` which is the same as what it would have been stored as in ASCII / ANSI
* Many different encodings
  * UCS-2 / UTF-16 which stores everything in two-bytes
  * UCS-4 which stores each code point in 4 bytes so that every single code point can be stored in the same number of bytes (but potentially wasting a lot of memory)
  * UTC-7, 8, 16, and 32 all can store any code point correctly
* If there's no equivalent for the Unicode code point you're trying to represent in the encoding you're trying to represent it in, that's when characters don't show up
* Don't have a string without knowing what encoding it uses
* Make usre to set appropriate `charset` when setting `Content-Type` headers
