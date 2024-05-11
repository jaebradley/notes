# [Bash Debugging](https://wizardzines.com/comics/bash-debugging/)

* `set -x` or `bash -x` to print out every line of the script as it executes
* `trap ‘(read -p “[$BASH_SOURCE:$LINENO] $BASH_COMMAND”)’ DEBUG`
  * `read -p` prints a message, press enter to continue
  * the message is the script filename and line number and next command to run
* die / fail function: `echo $1 >&2; exit 1;`
  * exit program and print a message if a command fails
  * `some_command || die "some error occurred"`
