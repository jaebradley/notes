# [A BIG Collection Of Unix/Linux "find" Command Examples](https://alvinalexander.com/unix/edu/examples/find.shtml)

* `-type f` to return files and `-type d` to return directories
* Use `-not` to invert a filename pattern like `-not -name "*.html"`
* `find` + `grep` like
  * `find . -type f -name "*.java" -exec grep -l StringBuffer {} \;`
  * The `{} \;` are required when `exec`ing a command on the files found - like a placeholder for each file that is found
* `find + exec` like
  * `find ... -exec chmod 644 {} \;` - applies the `chmod` command to each file that is found
* Combine `find` properties like `find . -type f \( -name "*.c" -o name "*.sh" \)
  * `-o` is for "or"ing these different name filters together
* Case-insensitively search files by using `-iname` instead of `name`
* To find files and directories that have been modified in the past 7 days use `find . -mtime -7`
* Find and copy files to a particular directory
  * `find . -type f -name "*.mp3" -exec cp {} /tmp/MusicFiles \;`
