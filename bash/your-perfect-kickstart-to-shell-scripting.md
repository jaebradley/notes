# [Your Perfect Kickstart To Shell Scripting](https://codeburst.io/your-perfect-kickstart-to-shell-scripting-857b81c0939b)

## Shebang

* Every script needs to start with a `#!/bin/bash` / `#!/bin/{X}` to select the correct interpreter
  * `#!` is called a "sharp" + "bang" i.e. a "shebang"
  * If a script does not contain a shebang, the commands are executed using your shell

## `read` Command

* `read -p "MESSAGE" VARIABLE
  * `MESSAGE` will be displayed on the screen and whatever is entered will be stored in `VARIABLE`

## Tests

* `-d` tests if file is a directory
* `-e` tests if file exists
* `-f` tests if file exists and is a regular file
* -`r` tests if file is readable
* `-s` tests if file exists and is not empty
* `-w` tests if file has write permission
* `-x` tests if file is executable
* `-z` tests if string is empty
* `-n` tests if string is not empty

## Case Syntax

```bash
case "$VAR" in
  pattern_1)
    # commands
    ;;
  pattern_2)
    # other commands
    ;;
  *)
    # default commands
    ;;
esac
```

## Loops

```bash
for VARIABLE_NAME in ITEM_1 ITEM_2
for (( VAR=1;VAR<N;VAR++ ))
```

## Positional Parameters

`./script.sh param1 param2 param3 param4`
  * `$0` is `script.sh`
  * `$1` is `param1` (etc)
  * `$@` is an array of all positional parameters

## Exit Statuses

* `0` means success - any code other than `0` means some kind of error condition
* `$?` contains the return code of the previously executed command

## Variables

* All variables, by default, have global scope. Having a global scope means that the value of that variable can be accessed from anywhere in the script
* Local variables can be accessed only from within the function
  * Local variables are created using the `local` keyword and only functions can have local variables

## Debugging

* `-x` option prints commands and arguments as they execute (print debugging / tracing / x-trace)
  * Add it to the shebang like `#!/bin/bash -xe`
* `-e` option stands for exit on error
* `-v` option prints shell commands as they are read

