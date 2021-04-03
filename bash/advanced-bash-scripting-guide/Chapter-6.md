# [Chapter 6: Exit and Exit Status]

* Every command returns an exit status - a successful command returns a `0` while and unsuccessful one returns a non-zero error code
* When a script ends with an exit that has no parameter, the exit status of the script is the exit status of the last command executed in the script (prior to the exit)
  * This behavior also occurs when an `exit` is omitted
* `$?` reads the exit status of the last command executed
