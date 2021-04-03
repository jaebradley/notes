# [Chapter 4: Introduction to Variables and Parameters](https://tldp.org/LDP/abs/html/variables.html)

## Variable Substitution

* Assignment may be with an `=`, in a `read` statement, and at the head of a loop (like `for variable in a b c`)
* It is nevertheless possible to perform arithmetic operations on an uninitialized variable
* A variable's name is in fact a reference / pointer to the memory location where the actual data associated with that variable is kept

## Bash Variables Are Untyped

* Bash bariables are character strings - Bash permits arithmetic operations and comparisons on variables if the value of a variable contains only digits
* Can turn an integer into a string by substituting part of the integer for a string replacement (in example, `2335` is turned into `BB35`)
  * Declaring a string as an integer doesn't do anything
  * Adding `1` to a string sets the variable as `1` since the "integer value" of a string is `0`
  * Same thing with doing arithmetic operatings on a "null" variable (`e=''`) or undeclared variables
* Can turn a string into an integer by substituting any non-digits with digits
  * Can then add values to integer


## Special Variable Types

* Each process has an "environment", that is, a group of variables that the process may reference
* Updating or adding new environmental variables causes the shell to update its environment, and all the shell's child processes (the commands it executes) inherit this environment
* If a script sets environmental variables, they need to be "exported", that is, reported to the environment local to the script
  * A script can export variables only to child processes i.e. only to commands or processes which that particular script initiates
  * A script invoked from the command-line cannot export variables back to the command-line environment
* Arguments passed to the script from the command lind `$0 $1 $2 $3 ...`
  * `$0` is the name of the script itself
  * `$1` is the first argument, etc.
* `$*` and `$@` denote all positional parameters
