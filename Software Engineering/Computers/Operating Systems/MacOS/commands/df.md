# `df`
* `df` reports how much disk space your filesystem is using

## Options
* `-h` for human-readable output
* `-i`: shows inodes used on each mounted filesystem
* `-c`: Display total
* `-k`: Use 1KB blocks
* `-Y`: Include file system type
* `-T {file system types}`: Select specific file systems to display. Multiple file systems can be specified via a comma-separated list.
* `df -HicY`: Human-readable, display inode usage, display total, display filesystem type
* `df` also takes a path argument to see the disk space available on a specific mount point
