# [Chapter 4: Introduction to Variables and Parameters]

* Each process has an "environment", that is, a group of variables that the process may reference
* Updating or adding new environmental variables causes the shell to update its environment, and all the shell's child processes (the commands it executes) inherit this environment
* If a script sets environmental variables, they need to be "exported", that is, reported to the environment local to the script
  * A script can export variables only to child processes i.e. only to commands or processes which that particular script initiates
  * A script invoked from the command-line cannot export variables back to the command-line environment
* Arguments passed to the script from the command lind `$0 $1 $2 $3 ...`
  * `$0` is the name of the script itself
  * `$1` is the first argument, etc.
* `$*` and `$@` denote all positional parameters
