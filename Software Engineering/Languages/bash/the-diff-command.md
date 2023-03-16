# `diff` command

## [Linux Commands: diff](https://flaviocopes.com/linux-command-diff/)

* Using the `-y` option compares files line by line
* The `-u` option is the same used by Git
* Use the `-r` option to compare directories
* `-rq` identifies the files which differ rather than the content

## [Introduction to the Diff Command](https://www.redhat.com/sysadmin/introduction-diff-command)

* `4a5,0` means that you have to add some lines after line `4` so that it looks like lines `5-7` in the second file
  * The opposite file combination will produce `5,8d4` which tells us that lines `5-8` need to be deleted to make the file identical
* The `c` means that the line has to be changed
