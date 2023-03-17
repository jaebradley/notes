# [Command Substition](https://tldp.org/LDP/abs/html/commandsub.html#COMMANDSUBREF)

* Command substition reassigns the output of a command or even multiple commands; it literally plugs the command output into another context
* Classic form of command substitution using backquotes ("`")
  * Can also do `$(command)`
* Command substitution invokes a subshell and it may result in word splitting
  * COMMAND `echo a b` - `a` and `b` are args to `COMMAND`
  * COMMAND "`echo a b`" - one argument to `COMMAND` (`a b`)
  * COMMAND `echo` - no args
  * COMMAND "`echo`" - one empty arg
* Command substitution can remove trailing new lines
* Write a program or script that outputs to `stdout` and assign that output to a variable

