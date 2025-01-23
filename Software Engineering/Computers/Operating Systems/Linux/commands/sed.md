# `sed`

* `sed` is short for “stream editor”
* Useful for find/replace, insertion, deletion

## Format
`sed [options] {command} file(s)`

## Options
* `-i`: edits file in-place
* MacOS expects a space afther the `-i` option like `sed -i ‘ ‘ ...`
* `-e`: can specify multiple commands
  * `-e` is not supported on MacOS
* `-r` to specify regex in command

## Substitute
* `s/pattern/replacement`: substitutes first occurrence of the pattern string with the replacement string
* `s/pattern/replacement/g`: substitutes all occurrences of the pattern string with the replacement string

## Delete
* Deletes lines that match the specified pattern
* `/apple/d file` - deletes all lines containing the word “apple”

## Print
* Print lines that match specified pattern, when combined with the `-n` option
* The `-n` option disables the automatic printing of the matching pattern
* `sed -n ‘/apple/p’ file` - print all lines that contain “apple”

## Insert/Append
* Inserts a line before the matching line
* `’/apple/i some line’ file` - inserts “some line” before very line containing “apple”
* append (`a`) works the same

## Change
* Changes the entire line to the specified replacement string
* `’/apple/c some replacement line’ file` - replaces any line that contains “apple” with “some replacement line”

## Tricks
* Can specify a line number or range to apply the command
* `sed ‘1,5s/apple/bananan/‘ file` - replace apple with banana only on lines 1 to 5
* Can also specify a single line number instead of a range
* For the last line in the file, can use `$`

