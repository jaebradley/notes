# [Subshells](https://tldp.org/LDP/abs/html/subshells.html#SUBSHELLSREF)

* A subshell is a separate instance of the command processor (the shell)
* Each shell script running is, in effect, a subprocess (child process) of the parent shell
* A shell script can itself launch a subprocess
  * Lets scripts do parallel processing, in effect executing multiple subtasks simultaneously

```bash
(
while [ 1 ]
do
  echo "Subshell running"
done
)
```

* The parentheses indicate a subshell
* Running the subshell and then looking at processes will see the process id from the running script and also a subprocess from the subshell which will have its own process id and will reference the running script's process id as its parent process id

## Command List within Parentheses

* `(command1; command2; command3;...)` - commands run as a subshell
  * Variables in a subshell are not visible outside the block of code in the subshell
  * They are not accessible to the parent process, to the shell that launched the subshell
  * They are local variables to the child process
* A subshell may be used to set up a "dedicated environment" for a command group
  * So within a subshell, set variables, execute a bunch of commands and then exit
  * The `exit` would only terminate the subshell and not the parent shell
* `(cat list1 list2 list3 | sort | uniq > list123) * (cat list4 list5 list6 | sort | uniq > list456) & wait`
  * Merges and sorts both sets of lists simultaneously - the `wait` ensures the next command is not executed until subshells finish
