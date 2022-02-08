# [Linux commands: xargs](https://flaviocopes.com/linux-command-xargs/)

* The `xargs` command is used in a UNIX shell to convert input from standard input into arguments to a commnd
  * `command1 | xargs command2` - `command2` will execute, using the output of `command1` as arguments
* So if there is a `some_file.txt` that has three lines, each with a file name, then `cat some_file.txt | xargs rm` will remove each of the files defined within `some_file.txt`
  * `xargs` will execute `rm` for each line returned by `cat`
* The `-p` option prints a confirmation prompt with the action it's going to take (i.e. `rm file1 file2`)
* The `-n` option lets you tell `xargs` to perform one iteration at a time (technically, `-n1`), so you can individually confirm them with `-p`
* The `-I` option iterates over the input with a "placeholder" variable like `command1 | xargs -I % /bin/bhs -c 'command2 %; command3 %'`
  * The `%` symbol can be swapped with something else
