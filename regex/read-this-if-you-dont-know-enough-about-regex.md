# [Read This If You Don't Know Enough About Regex](https://levelup.gitconnected.com/read-this-if-you-dont-know-enough-about-regex-73141bb0e1a7)

* To search through text for string `hij` use regex like `/hij/` - the forward slashes indicate the start and end of the regular expression
* To see if regex matches at the start of the string start the regex with `^` (`/^abc/`)
* To match at the end of the string use `$` like `/xyz$/`

## Escaping Characters

* There are special characters that need to be escaped (i.e. characters that have special meaning in regex)
* These special characters are `[`, `\`, `^`, `$`, `.`, `|`, `?`, `*`, `+`, `(`, `)`
* If we want to search for a question mark, in regex, the question mark has a special meaning, so to do a literal search for a question mark, it needs to be escaped (`\?`)

## Flags

* There are a variety of flags that are used at the end of the regex (after the last `/` character)
* The global flag (`/some regex/g`) takes all matches instead of returning just the first match
* The case-insensitve flag (`/some regex/i`) case-insensitively matches
* The `/m` flag allows searching over multiple lines

## Character Classes

* `\d` - matches any digit
* `\w` - matches a letter from the latin alphabet, a digit, or an underscore
* `\s` - matches "space" symbpols includings spaces, tabs, and newlines
* All character classes have an inverse, which is the same letter but uppercased so `\W` is non-wordy character, and `\D` is a non-digit character

## Quantifier

* `?` means zero or one so `/ba?/g` means try and match any `b` character followed by zero or one `a` characters
* `*` means zero or more
* `+` means one or more
* `/ba{2}/g` regex matches any `b` character that is followed by 2 consecutive `a` characters
  * `/ba{2, 4}` regex matches any `b` character followed by two to four consecutive `a` characters