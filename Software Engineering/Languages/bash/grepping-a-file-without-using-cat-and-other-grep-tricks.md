# [Grepping a file without using cat, and other grep tricks](https://bencane.com/2013/08/19/grepping-a-file-without-using-cat-and-grep-other-tricks/)

* Use the `-v` flag to omit the searched string and return everything else
  * `ls | grep -v ".log" | xargs rm` - remove all files in the current directory except the ones with `.log` in their filename
* Use the `-c` flag to count the occurrences in a file
* Use the `-r` flag to search recursively in multiple directories
* Use the `-l` flag to output the filenames of files that match the string
* Use the `-L` flag to identify files that _don't_ contain the search term
* Use the `-i` flag to search case insensitively
