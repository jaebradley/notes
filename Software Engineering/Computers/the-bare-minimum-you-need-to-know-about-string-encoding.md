# [The Bare Minimum You Need To Know About String Encoding](https://levelup.gitconnected.com/system-design-interview-the-bare-minimum-you-need-to-know-about-string-encoding-6d57ce139942)

* Unicode is not an encoding scheme - it does not govern how characters are stored in bytes
* Unicode defines a 1-1 mapping from a character to a code point
* Then use an encoding scheme to encode the code point in bytes
* UTF-32 is nice and easy, but wastes bytes if your characters are mostly ASCII (which falls in the range of 0 to 127)
* UTF-8 is not always 1 byte per character
  * It tries to fit code points in 1 byte, which works for all ASCII characters
  * When a code point can't fit into 1 byte, UTF-8 uses 2-4 bytes
  * UTF-8 is a variable length encoding
  * To tell how many bytes are used for a character, UTF-8 reserves the higher bits to signify this
    * `0xxxxxxx` is 1 byte
    * `110xxxxx 10xxxxxx` is for 2 bytes, etc
* A byte order mark for Unicode is included at the beginning of a byte stream to signify the endianness of the data
  * Less useful for UTF-8 as UTF-8 reads 1 byte at a time
  * But for UTF-16 and UTF-32, you'll read 2 or 4 bytes at a time
  * The BOM lets tells you whether it needs to reverse the bytes inside each word to match the endianness of the host system
