# [Data Wrangling](https://missing.csail.mit.edu/2020/data-wrangling/)

* `ssh myserver 'journalctl | grep sshd | grep "Disconnected from"' | less`
  * Logs may be large, so stream and filter them on the remote server, and then analyze data locally
  * Could output filtered logs to a file to avoid having to access the network (replace piping to `less` with `> output.log`)
* `sed` (stream editor)

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed 's/.*Disconnected from //'
```

* The `s` (substitution) command will substitute the `Disconnected from ` string with nothing
* The `-E` command interprets extended regex

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
```

* This gets the list of distinct usernames that have attempted to login, sorted, and then counted
  *  `uniq -c` collapses consecutive lines that are the same into a single line, prefixed with a count of the number of occurrences

```bash
ssh myserver journalctl
 | grep sshd
 | grep "Disconnected from"
 | sed -E 's/.*Disconnected from (invalid |authenticating )?user (.*) [^ ]+ port [0-9]+( \[preauth\])?$/\2/'
 | sort | uniq -c
 | sort -nk1,1 | tail -n10
```

* To sort previous list by the most common usernames
  *  `sort -n` will sort in numeric (instead of lexicographic order) and this is useful since previous distinct usernames were prefixed with a count
  *  `sort -k1,1` means sort by only the first whitespace-separated column, until the first field (defaulting to end of the line)
  *  To get least common usernames, could use `head` instead of `tail`
  *  `sort -r` sorts in reverse order

## `awk`

* `awk` programs are an optional pattern + block executing some instructions to pattern matches a given line
* Default pattern (like in `awk '{print $2}'`) matches all lines
* Inside the block, `$0` is set to the entire line's contents
 * `$1` to `$n` are set to the `nth` field of the line, when separated by the field separator (whitespace by default, can be set with `-F`)
 
```bash
awk '$1 == 1 && $2 ~ /^c[^ ]*e$/ { print $2 }' | wc -l
```

* Single-use usernames that start with `c` and end with `e`
* `$1 == 1` means that the `uniq`d `count` is `1` (single-use)
* `^c[^ ]*e$`
 * Begins with `c`
 * `[^ ]` is a negated set meaning that match any character not that is a space
 * Match any other tokens
 * End with `e`
* `$2 ~ {some regex}` means that the second field should match the given regex
* The "block" instructions are to print out the second field / username
* `wc -l` counts the number of output lines
