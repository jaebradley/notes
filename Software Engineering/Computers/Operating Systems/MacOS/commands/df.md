# `df`
* Displays a report of the system’s disk usage
* For each listed file system, `df` displays its name, size, used space, available space, % of space used, and where the file system is mounted
* Can specify a specific file or directory as an argument like
  * `df /path/to/file/or/directory`
* `df` looks at the block allocation tables and counts how many blocks are used/free
  * In contrast, `du` will calculate the size of each individual file
  * So a `25` byte file will use 1 block from `df`'s perspective, but will use `25` bytes from `du`’s perspective

## Options
* `-h`: Displays values in human-readable format
* `-c`: Display a grand total
* `-i`: Include statistics on the free and used inodes
* `-Y`: Include file system type
* `-,`: Numerical separator (typically, comma or period)
