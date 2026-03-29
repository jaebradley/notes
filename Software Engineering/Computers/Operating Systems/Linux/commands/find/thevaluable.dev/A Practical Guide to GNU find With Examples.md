# [A Practical Guide to GNU find With Examples](https://thevaluable.dev/find-cli-guide-examples/) 
* General syntax is `find [options] [directory] [expressions]`
  * The default directory is the current directory
  * Expressions are always prefixed with a single dash
* Example command: `find mouseless -name '*.jpg' -perm '664'`
  * Start searching the `mouseless` directory, including subdirectories
  * `-name` specifies searching files by their name - in this case a wild-carded expression is used that matches `jpg` file extensions
    * Single quotes were intentional to avoid the shell expanding the `*` wildcard
  * `-perm` specifies searching files by a permission value
  * These filters are *anded* together, by default
* The `-empty` filter will only find empty files and directories
* The `-path` filter can be used to filter files and directories depending on the filepath
  * Does not accept regex but *does* accept glob operators
* For regex, use the `-regex` filter, like `-regex '.*(txt|jpg)$'` 
  * The extended regex using `egrep` is more powerful and can be specified with the `-regextyp 'egrep'` option
* Use `-iname` to search case insensitively
* Use `-type f/d` to search for regular files or directories, respectively
* Permissions can be searched by octal number, as seen above, but also by using `-writable`, `-executable`, and `-readable` filters
* The `-exec` expression executes the defined command on each file found
  * The `{}` are used as a placeholder for each result
  * The `;` character is used to end the command
  * `find . -type f -exec 'rm -i' '{}' ';'` would prompt the user to delete each file
* The `-ok` expression will force find to prompt the user and make sure that the user wants to execute the command for each file
* `-print0` is useful when piping `find`s results to `xargs -0` to avoid problems with whitespace
* Can also print directly to files by using the expression `-fprint0`, `-fprintf`, etc
* Can use the `-or` and `-and` operators to combine expressions
* The `!` operator reverses an expression
