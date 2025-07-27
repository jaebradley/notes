# [Getting decent error reports in Bash when you're using 'set -e'](https://utcc.utoronto.ca/~cks/space/blog/programming/BashGoodSetEReports)
* `set -e` so the script immediately stops on unexpected errors from commands
* `trap 'echo "Exit status $? at line $LINENO from: $BASH_COMMAND"' ERR`
* `$BASE_COMMAND` environment variable stores the command executed before the trap
* `trap` condition executes the `trap` statement when `set -e` tells the script to fail and exit
* Use `ERR` instead of `EXIT` in order to get the relevant line number in bash
  * `trap...EXIT` will report the line the `trap` was defined on, not the line of the failing command
* No other versions of Bourne shell can do this
  * If you want Bash use `#!/bin/bash`
