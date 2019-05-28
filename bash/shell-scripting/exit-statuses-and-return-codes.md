# Exit Statuses and Return Codes

* Exit status is integer between `0` and `255`
* Commands that are successful with exit with a `0` status
* Failed commands will exit with a non-zero status
* Special variable `$?` contains return code of previously executed command
* Command following a `||` (`OR`) will only execute if previous command fails

## `exit` command

* If you do not specify a return code with the `exit` command, the exit status of the previously executed command is used
* `exit` can be used anywhere - when reached, script will stop running
