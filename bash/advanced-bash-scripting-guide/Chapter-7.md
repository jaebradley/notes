# [Chapter 7: Tests](https://tldp.org/LDP/abs/html/tests.html)

## Test Constructs

* Bash sees `[[ $a -lt $b ]]` as a single element, which returns an exit status
* An `if` can test any command, not just conditions enclosed in brackets
  * `if grep -q some string` then do something
* Some truthiness results
  * `0` is truthy
  * `1` is truthy
  * `-1` is truthy
  * `NULL` / `[]` is falsy
  * "xyz" is truthy
  * An uninitialized variable is false
    * Can do this by doing `if [ $not_initialized ]` or more correctly `if [ -n "$not_initialized" ]`
  * An initialized variable that is set to a null value is also falsy
  * "false` is truhty
* The `((..))` construct expands and evaluates an arithmetic expression
  * If the expression evaluates as zero, it returns an exit status of `1`
  * A non-zero expression returns an exit status of `0`


## File Test Operators

* `-e` - file exists
* `-f` - file is not a directory or device file
  * The `/dev` directory contains entires for the physical devices that may or not be present in the hardware. These are called "device files"
  * For example, the hard drive partitions containing the mounted filesystem(s) have entries in `/dev`
* `-s` - file is not zero in size
* `-d` - file is a directory
* `-b` - file is a block device
  * A block device reads and/or writes data in chunks / blocks (hard drives, CD-ROM drivces, flash drives)
* `-f` - file is a character device
  * Character devices access data in character units (keyboards, modems, sound cards)
* `-p` - file is a pipe
* `-h` - file is symbolic link
* `-S` - file is a socket
* `-r` - file has read permission for user running test
* `-w` - file has write permission for user running test
* `-x` - file has execute permission for user running test
* `-O` - check if you are owner of file
* `-N` - check if file was modified since last read
* `file1 -ef file2` - check if files are hard links to same file

## Other Comparison Operators

#### Integer Comparison

* `-ne` - not equal to
* Bracket comparisons like `<` / `<=` are only used within double parentheses

#### String Comparison

* `=` - equal to
  * `"$a" = "$b"` is not the same as `"$a"="$b"`
* `<` / `>` take into account ASCII alphabetical order
* `-z` - string is null / has zero length
* `-n` - string is not null
  * Requires string is quoted within the test brackets
* `-a` and `-o` are logical and, or, respectively
  * However, they do not short-circuit like `&&` and `||` short-circuit
