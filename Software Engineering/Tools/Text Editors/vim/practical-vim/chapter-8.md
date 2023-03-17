# Chapter 8

* `ge` moves backward to end of previous word
* `ea` append at end of current word
* A `word` is a sequence of letters, digits, and underscores, or as a sequence of other nonblank characters separated by whitespace
  * Periods and apostrphes count as words, for example
* A `WORD` is a sequence of nonblank characters separated wtih whitespace
* `t` and `T` will search until a specific character (i.e. one character before)
* `f` and `F` will search on top of the specified character

## Operating with search motion

* Deleting a phrase might consist of
  * Going to visual mode
  * Selecting text until the start of word you don't want to delete (using search)
  * `h` since probably off by one
  * And then `d`
* Can also do `d/{searchText}` since the search command is an exclusive motion (i.e. it excludes the character at the start of the search)


## Vim Text Objects

* Consist of two characters
  * First is always `i` or `a`
    * `i` selects inside delimiters while `a` includes delimiters
* `i/a)` or `i/ab` - pair of parentheses
* `i/a}` or `i/aB` - pair of braces
* `i/a]` or `i/a]` - par of brackets
* `i/a>` - pair of angle brackets
* `i/a'` - pair of single quotes
* `i/a"` - pair of double quotes

## Delete around or change inside

* `iw` - word
* `iW` - WORD
* `is` - sentence
* `ip` - paragraph

## Mark Your Place and Snap Back to It

* Lowercase marks are local to each individual buffer while uppercase marks are global
* `'{mark}` moves to the line where a mark was set, positioning the cursor on the first non-whitespace character
* `backtick{mark}` moves the cursor to the exact position where the mark was set
* `mm` and `backtick+m` sets the mark `m` and then jumps to it
* Automatic marks
  * `"` - Position before last jump within current file
  * `backtick+.` - Location of last change
  * `backtick+^` - Location of last insertion
  * `backtick+[` - Start of last change or yank
  * `backtick+]` - End of last change or yank
  * `backtick<` - Start of last visual selection
  * `backtick>` - End of last visual selection

## Jump Between Matching Parentheses

* The `%` command jumps between opening and closing set of parentheses
