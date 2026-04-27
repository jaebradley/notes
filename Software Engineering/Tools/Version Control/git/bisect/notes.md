# `git bisect` notes
* `git bisect start` to initiate bisecting
* `git bisect bad HEAD` (mark `HEAD` as a "bad" (buggy) commit; could be a commit hash as well)
* `git bisect good {commit hash}`
* `git` will check commit in the middle of the remaining range and then developer can run tests, etc, to determine whether the current commit is good or bad (\`git bisect good\`, `git bisect bad`)
* Once the culprit commit is identified, can use `git bisect reset` to finish bisecting
* `git bisect run` with a reference to a test script or command
  * `git bisect run ./test_script_abc.sh`
  * `git` will automatically iterate through the commits, running the script for each one
    * Exit code `0` indicates the commit is good
    * Exit code `1-127` (inclusive) that is **not** `125` indicates the commit is bad
    * Exit code `125` is a special exit code that means "skip this commit"
    * Any exit code > `127` will abort the bisect process
* Can also specify a file name or path to `git bisect start` in the case where it is known that the bug comes from a particular set of files
  * `git bisect start some/working/directory`
  * Only the commits that changed the contents in this directory will be checked
* If it is difficult to tell if a particular commit is good or bad, execute `git bisect skip` which will ignore the commit
* Use the terms `old/new` instead of `bad/good` to understand what commits are associated with a fix (where `bad` and `good` might be confusing terms from a semantic standpoint).
  * Need to run `git bisect start` without commits as arguments and then add commits in following commands like `git bisect old <commit>` and `git bisect new <commit>`
