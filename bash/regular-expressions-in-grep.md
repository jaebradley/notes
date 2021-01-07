# [Regular Expressions In Grep](https://linuxize.com/post/regular-expressions-in-grep/)

* Use `^` or `$` to anchor a match in a line
  * `^` matches the emtpy string at the beginning of the line so `^linux` would match only if "linux" begins at the start of a line
  * `^linux$` would match lines that contain only "linux"
  * `^$` matches all empty lines
* Bracket expressions allow for matching groups of characters
  * So `acce[np]t` would match `accept` or `accent`
  * `co[^l]a` would match any lines that have strings starting with `co` followed by any letter except `l`, followed by `a`
  * Can also specify ranges like `[A-Z]` or `[1-3]`
  * Can also do `[:alpha:]` for alphabetic characters, `[:alnum:]` for alphanumeric characters, `[:blank:]` for spaces and tabs, `[:digit:]` for digits, `[:lower:]` for lowercase letters, and `[:upper:]` for uppercase letters
* `*` character matches preceding item zero or more times
* The `+` character matches the preceding item one or more times
* The `?` character makes the preceding item optional and it can match only once
* Grouping is a feature of regular expressions that allow you to group patterns together and reference them as one item
  * `(fear)?less` matches both `fearless` and `less` - the `?` makes the `fear` group optional
