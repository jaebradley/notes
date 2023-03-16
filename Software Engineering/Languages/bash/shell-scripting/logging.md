# Debugging

* `-x` options prints commands and their arguments as they are executed
  * called "print debugging", "tracing", "x-trace"
  * can set it in shebang like `#!/bin/bash -x`
  * instead of `echo "$SOME_VARIABLE"` it'll output `echo TEST`
* Can turn `-x` option on for portion of script
  * `set -x` in script and then when you want to turn it off, `set +x`
* `-e` option which causes script to exit immediately if command exits with non-zero status
  * Can combine with `-x`
* `-v` option prints everything before substitions and wildcard expansions are applied - basically what the line looks like in the shell script
  * When combined with `-x` you can see what the LOC looks like and then what it looks like when replaced by variables

## `PS4`

* Environment variable that is displayed before each command during an `-x` trace
* By default it's a `+`
* But can use variables like `$BASH_SOURCE` and `$LINENO` (script name and line number in script, respectively)
  * `PS4='+ $BASH_SOURCE: $LINENO` will lead to output like `+ ./test.sh : 3 : TEST_VAR=test`

**Run `cat -v script.sh` to display non-printable characters, like carriage returns**

