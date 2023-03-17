# Chapter 2

## Deleting a word from the last character

* `dbx`
  * `db` deletes from cursor to beginning of word (leaving last character intact)
  * `x` deletes the last character
* `bdw`
  * `b` goes to beginning of word and then `dw` deletes word
* `daw`
  * `aw` selects a word (including whitespace)
  * `d` deletes the selected word
* `daw` can be repeated using dot command whereas other two combinations will only repeat last operation `x` or `dw`

## Performing simple arithmetic

* `Ctrl - a` and `Ctrl - x` perform addition and subtraction on numbers
  * Can combine with a number to increment accordingly
  * Will look for number at or after cursor

## Operators

* `d{motion}` can operate on single character (`dl`) a complete word (`daw`) or an entire paragraph (`dap`)
* Commands that need some form of defined motion are called operators
* Common operators include
  * `c` - Change
  * `d` - Delete
  * `y` - Yank into register
  * `g~` - Swap case
  * `gu` - Make lowercase
  * `gU` - Make UPPERCASE
  * `>` - Shift right
  * `<` - Shift left
  * `=` - Autindent
  * `!` - Filter {motion} lines through external program
* An action is composed from an operator followed by a motion
* When an operator command is invoked in duplicate, it acts upon the current line
  * `dd` deletes the current line
  * `>>` will indent the current line
  * `gU` will act upon the current line by running `gUgU` or `gUU`

