# [Coming to grips with grep](https://www.admin-magazine.com/Archive/2014/24/Coming-to-grips-with-grep)

* `grep -c some_pattern some_file` - Count how many times `some_pattern` shows up in `some_file`
* `zgrep` can look through compressed files and return pattern matches
* `grep -r` - recursively searches (even dotfiles)
* `grep -l` returns only the filenames of the files that return a positive hit (as opposed to the full line of content that has a match)
  * `-L` will only get files without the pattern (i.e. files that return a negative hit)
* `grep -i` means that `grep` should ignore all case sensitivity
* `grep -o` will only output the strings of interest, which is useful when matching a pattern
  * `grep -o "start-.*-end" some_file` will only return the words that match the pattern (instead of whole lines)
* The `-b` flag returns the byte offset of a given match
* The `-n` flag reports the line number of the pattern appearing in the file
* The `-A` and `-B` switches lets you see lines before / after a match
