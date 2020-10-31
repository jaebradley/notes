# [Linux And Unix xargs Command Tutorial With Examples](https://shapeshed.com/unix-xargs/)

* `xargs` is a command line utility for building an execution pipeline from standard input
  * While `grep` can accept standard input, things like `echo`, `rm`, and `mkdir` do not take standard input as arguments
* `xargs` reads items from standard input as separated by blanks and executes a command once for each argument
* `echo 'one two three' | xargs mkdir` - standard input is piped to `xargs` and the `mkdir` command is executed for each argument
* The `-t` option prints each command that will executed to the terminal
* The `-p` command will print the command to be executed and prompt the user to run it
