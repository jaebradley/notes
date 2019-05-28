# Functions

* Can create a function explicitly using keyword `function` followed by function name and a set of parentheses
* Can also create function without using the `function` keyword
* Can access values passed as params using `$1`, `$2`, etc.
  * `$0` is name of script itself
  * Can loop through all passed parameters like `for NAME in $a`

## Variable Scope

* By default, all variables are global
* Variable and its value can be accessed from anywhere, including in any function
* Variable has to be defined before use
* If a global variable is defined in a function, it is not available outside that function until the function is called and executed
* Local variable is a variable that can only be accessed within the function in which it is declared - use `local` keyword before variable name
  * Only need to use `local` keyword the first time local variable is used
  * `local` keyword can only be used inside function

## Exit status and return codes

* If no `return` statement is used, exit status of last command is exit status of function
* `return` statement only accepts integer from `0` to `255`

