# Shell Scripting, Succinctly

* The shebang (`#!`) determines the interpreter
  * What follows after the shebang is used as the interpreter for commands in the script
  * `#!/bin/csh`
  * `#!/bin/ksh/`
  * All examples of different interpreters
  * Interpreter is executed and the path used to call the script is passed as an arg to the interpreter
* Without a shebang the commands in the script will be executed using current shell

## Variables

* When defining variables, do not use spaces before or after equals sign
* Variables are case-sensitive and convention is to use UPPERCASE variable names
* To use a variable precede the variable name with a dollar sign
* Can also use curly braces to enclose variables unless interpolating the variable / combining it with additional data
* Can also assign output of command to variable
  * Need to enclose command in parentheses and precede it with a dollar sign
  * `SOME_VARIABLE=$(somecommand)`

## `if` statement

* Form of if statement is basically

```bash
if [condition]
then
  # do some stuff
elif [other condition]
then
  # do some other stuff
else
  # do some other other stuff
fi
```

* Important to note ending `fi`
* Best practice is to enclose variables in quotes

## `for` loop

* Form is basically 

```bash
for VARIABLE_NAME in ITEM_1 ITEM_2 ITEM_N
do
  # some stuff
done
```

* First item in list is assigned to variable and code block is executed, then next item is assigned and command is executed, etc. etc.

## Positional Parameters

* Variables `$0` through `$9`
* The script is stored in `$0`
* The first parameter is stored in `$1` etc.
* can access all positional parameters starting with `$1` to very last one using `$a`

## User Input

* Use `read` command to get standard input
  * Format is usually `read -p "SOME PROMPT" SOME_VARIABLE_NAME

