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
* `{startRange, endRange}` - matches number of patterns in defined range
  * `c{2, 5}` - matches `c`s between `2` and `5`
