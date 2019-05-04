# [Rock the Command Line](https://towardsdatascience.com/rock-the-command-line-52c4b2ea34b7)

* `ps` will print all running processes associated with user's shell
  * Use `-e` to print all running processes
* `cp source_file destination_directory` - copy file and put it in destination directory
* `ln -s source_file target_file` to symbolically link files
  * When source file is updated then destination file is updated
  * When destination file is updated then source file is updated
  * When source file is deleted, destination file still exists but is empty
  * When destination file is deleted, source file exists
  * Can do this with directories as well
* `command < file` to output the standard input to file
* `command > file` to output the standard output to file
  * Both `<` and `>` create file if it doesn't exist and overwrite the existing file contents if it does
  * `>>` to append instead of overwriting
* `cat file1 file2` will concatenate both files and output to standard out
* `ag`
  * `ag some_regex some_file` to return matches (with line numbers)
  * Use `-i` flag to do case-insensitive matching
  * Will automatically read `.gitignore` to ignore any matching files or folders
  * `--skip-vcs-ignores` will ignore `.gitignore`
