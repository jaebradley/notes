# [Git Searching](https://git-scm.com/book/en/v2/Git-Tools-Searching)

* `git grep` allows searching through any committed tree, the working directory, or even the index
* Can take text or regex
* By default, looks at files in working directory
* Use `-n` or `--line-number` to print out line numbers where Git finds matches
* Can show counts of matches in files by using `-c` or `--count` flag
* Can show context of a match using `-p` or `--show-function` flag
* Can add `--header` and `--break` flags for more readable output
* `--and` flag for combinations
  * `git grep -e '#define' --and \( -e LINK -e BUF_MAX \)`
  * This will look for a line that defines a constant with a name of `LINK` or `BUF_MAX`
* `git log -L` will show history of a function or line of code in codebase
  * `git log -L :some_function_name:some_file_name` will try and find bounds of function and then look through history and show every change made to function
  * Can also give it a range of lines or a single line number (`git log -L start,end:file_name`)

