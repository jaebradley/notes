[# The Different Modes for Opening Files](https://stackoverflow.com/a/23566951/5225575)

* `r`
  * Opens a file for reading only
  * File pointer starts at beginning of file
  * Default setting for `open` (for example)
* `rb`
  * Opens a file for reading only in binary format
  * File pointer starts at beginning of file
* `r+`
  * Opens a file for both reading and writing
  * File pointer starts at beginning of file
* `rb+`
  * Opens a file for reading and writing in binary format
  * File pointer starts at beginning of file
* `w`
  * Opens a file for writing only
  * Overwrites any existing file
  * If the file does not exist, creates a new file for writing
* `wb`
  * Opens a file for writing only in binary format
  * Overwrites any existing file
  * If the file does not exist, creates a new file for writing
* `w+`
  * Opens a file for both reading and writing
  * Overwrites any existing file
  * If the file does not exist, creates a new file for reading and writing
* `wb+`
  * Opens a file for both reading and writing bytes
  * Overwrites any existing file
  * If the file does not exist, creates a new file for reading and writing
* `a`
  * Opens a file for appending
  * File pointer is at end of file
  * If the file does not exist, creates a new file for writing
* `ab`
  * Opens a file for appending bytes
  * File pointer is at end of file
  * If the file does not exist, creates a new file for writing
* `a+`
  * Opens a file for both appending and reading
  * File pointer is at end of file
  * If the file does not exist, creates a new file for both reading and writing
* `ab+`
  * Opens a file for both appending and reading bytes
  * File pointer is at end of file
  * If the file does not exist, creates a new file for both reading and writing
