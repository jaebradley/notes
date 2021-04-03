# [Chapter 5: Quoting](https://tldp.org/LDP/abs/html/quoting.html)

## Quoting Variables

* It is generally advisable to enclose its name in double quotes
  * This prevents reinterpratation of all special characters within the quoted string except `$`, backtick, and escape
  * Double quotes also prevents word splitting - an argument enclosed in double quotes presents itself as a single word even if it contains whitespace separators

```
list="one two three"
for a in $list
# Will print `one`, `two`, `three`

for a in "$list"
# Will print `one two three`
```

* Single quotes (or full quoting) is a stricter method of quoting than double quotes (partial quoting) because single quotes do not permit any special characters except the single quote


## Escaping

### In `echo` and `sed`

* `\r` - return
* `\t` - tab
* `\v` - vertical tab
* `\b` - backspace
* Use `-e` with `echo` to print escaped characters
* `echo -e "\042"`
  * Prints octal ASCII character 42
* `\"` gives the quote its literal meaning
  * `echo "\"Hello\"` results in "Hello"
  * Similarly, `\\` gives backslash its literal meaning
