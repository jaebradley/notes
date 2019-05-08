# [Regex Tutorial: A Quick Cheatsheet by Examples](https://medium.com/factory-mind/regex-tutorial-a-simple-cheatsheet-by-examples-649dc1c3f285)

## Anchors

* `^{start}` - Matches any string that starts with `start`
* `{end}$` - Matches any string that ends with `end`
* `^{start} {end}$` - Matches any string that starts and ends with `{start} {end}`
* `{text}` - Matches any string that has `{text}` in it

## Quantifiers

* `*` - matches zero or more
  * `c*` - matches zero or more `c`
  * `a(bc)*` - matches string with `a` followed by zero or more copies of `bc`
* `+` - matches one or more
* `?` - matches zero or one
* `{number}` - matches number of pattern
  * `c{2}` - matches `2` `c`s
* `{number,}` - matches number or more of pattern
  * `c(2,}` - matches `2` or more `c`s
* `{startRange, endRange}` - matches number of patterns in defined range
  * `c{2, 5}` - matches `c`s between `2` and `5`
  * Can also apply this to groups like `a(bc){2, 5}` which would match string with between `2` and `5` copies of `bc` sequence
* `a(b|c)` matches string with `a` followed by `b` or `c`
  * Can also be written `a[bc]`

## Character Classes

* `\d` matches a single character that is a digit
  * `\D` is inverse (single _non-digit_ character)
* `\w` matches a word character (alphanumeric and underscore)
  * `\W` is inverse (single _non-word_ character)
* `\s` matches whitespace character (tabs and linebreaks)
  * `\S` is inverse (single _non-whitespace_ character)
* `.` matches any character
* In order to be taken literally, `^`, `.`, `[`, `$`, `(`, `)`, `|`, `*`, `+`, and `?` need to be escaped
  * `/$/\d` matches strings with dollar-sign followed by a digit

## Flags

* Regex often is delimited by two `/` (`/{my regex}/{flag}`)
* `g` is a global search flag and does not return after the first match
* `m` is a multiline flag that will match start and end of line when using `^` and `$`
* `i` is a case-insensitive flag

## Grouping and Capturing

* Groups are exposed in an array unless a specific name is specified for a group and then can access dictionary
* `a(bc)` creates a capturing group with value `bc`
* Can ignore a capture group using `?:`
  * `a(?:bc)`
  * Might want to use capture group for matching but not care about having it in the groups array
* Can name a capture group like `a(?<foo>bc)`

## Bracket Expressions

* `[abc]` matches a string that has either an `a` or a `b` or a `c` in it
  * Can also be expressed as `[a-c]`
* `[a-fA-F0-9]` matches single hexademical digit, case insensitively
  * `a-f` and `A-F` and `0-9`
* Can negate using `^`
  * `[^a-zA-Z]` means not a letter from `a-z` nor from `A-Z`

## Greedy and Lazy Matching

* `*`, `+` and `{}` are greedy operators so they expand the match as far as they can
* `<.+?>` matches any character one or more times included inside a `<` and a `>`
  * Better solution is `<[^<>]+>` which matches any character except `<` or `>` one or more times included inside a `<` or `>`

## Look-ahead and Look-behind

* `d(?=r)` matches a `d` that is before an `r` but `r` will not be part of regex match
  * Can negate like `d(?!r)` which matches `d` only if it is not followed by `r`
* `(?<=r)d` matches a `d` that is preceded by an `r` but `r` will not be part of regex match
  * Can negate like `d(?<!r)` which matches `d` only if it is not preceded by `r` 

