# [A Practical Guide to GNU sed With Examples](https://thevaluable.dev/sed-cli-practical-guide-examples/)
* General syntax is `sed [script] [file(s)]`
* `sed '' some file`  will print every single line in `some file` as the sed "script" is empty
  * Equivalent to `cat some file` 

## The Three Parts of a `sed` Script
* Address: the lines to edit
* Command: the operation to perform - always a single letter
* Options: commands may have options, like the substitute command
* By default, the command of a `sed` script will execute for each line
  * So `sed 'd' some file` will delete every line read from the file and output empty content
* Can use the `-i` option to modify the file in-place
  * `sed -i '1d' some file` will delete the first line of `some file` in-place

## The Address: Selecting Specific Lines
* Can be a line number, range of lines, every Nth line, regex
* This address *must* be defined the command associated with a `sed` script
* `sed '2,5d'` will delete lines 2 to 5, inclusive
* `sed '0~2d'` will delete every 2 lines starting from line 0
* `sed '/include/d'` will delete each line that includes `include`
* `sed '3,/include/d'` will delete each line from the third line until the first line that includes `include`
* `sed '/http/,/include/d'` will delete each line from the first line matching `http` to the first line matching `include` 
* For regex matches, can use the `I` flag to make the regex case insensitive
  * `sed '/HTTP/,/INCLUDE/Id'` 
* The extended regex engine has access to regex metacharacters (like `?`)
  * Add the `-E` option to `sed` to access the extended regex engine
* `sed '/server\|include\|on\|log\|^$/d'` (the default basic regex engine) is equivalent to `sed -E '/server|include|on|log|^$/d'` 
* The address can be inverted as well by using the `!` after the defined address
  * `sed '1!d'` will delete every line except the first line

## Only Output the Chosen Lines
* `-n` or `--quiet` or `--silent` will disable `sed` from outputting every line of the input, by default
* The `p` command prints only the addressed lines
* So `sed -n '1p' some file` will only print the first line of `some file` 
  * Without `-n` / `--quiet` the first line and *all lines* would be outputted to standard output
* Can mimic the behavior of `grep` by only outputting lines that match specific patterns
* Note that the following commands are equivalent
  * `sed '1!d'`  and `sed -n '1p'`

## Multiple Commands
* Separating commands with `;` will execute multiple commands
* `sed '1,10d;15,$d'` will delete lines 1-10 and then delete lines from line 15 to the end of the input
* The `-e` option can also be used to execute multiple scripts
  * The equivalent to the `;`-separated commands would be `sed -e '1,10d' -e '15,$d'`
 
## The Substitute Command
* Syntax of the substitute command is `s/<pattern>/<replacement>/[flag]`
* By default, only the first matching pattern will be replaced
  * To replace _all_ matches, use the global flag like `sed 's/some pattern/some replacement/g'`
* Can use other characters as separators, like `|`, `%`, or `#`
* To only print lines that will be replaced: `sed -n 's/some pattern/some replacement/gp'`
