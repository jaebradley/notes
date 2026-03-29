# find

* Syntax is `find [path] [expression]`
* Wildcards for things like name `(-name)` option are `*` and `?`
  * `find /path -name “somefilename?.txt”`
  * `find /path -name “*.txt”`
* Use `-iname` for case-insensitive search
* Use different file types to filter searches using the `-type` option
  * `f` - regular files
  * `d` - directories
  * `l` - symbolic links
  * `b` - block devices
  * `c` - character devices
* Can filter searches by file size using the `-size` option
  * `+X{unit}` means greater than `X` units of memory
  * `-` for less than
  * Unsigned means an exact match
  * Units can be blocks, bytes, `k`ilo bytes, `M`egabytes, and `G`igabytes
* Can filter searches by modification time using the `-mtime` option
  * Similar to `-size` the sign means more than / less than `X` days ago
* Can search by permissions (`-perm`)
* Can search by empty files or directories (`-empty`)
* `-and`, `-or` and `!` (negation) operators when combining filters
* Can run commands on the found files using the `-exec` option
  * `{}` is replaced by each found file
  * `\;` marks the end of the command
  * `find /path -name “*.log” -exec rm {} \;`
    * Delete all `.log` files
  * Example of how to combine with `grep` to search for a search string within files
    * `find /path -type f -exec grep -l “search string” {} \;`
* Limit the depth of search by using the `-maxdepth` and `-mindepth` options
