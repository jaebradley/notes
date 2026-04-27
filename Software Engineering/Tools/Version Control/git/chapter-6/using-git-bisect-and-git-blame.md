# Using `git bisect` and `git blame`

* Use case for `git bisect` is to help isolate changes that caused an application from going from a working state to a broken state
  * You know a definite start point (i.e. commit) where the application was working and a definite end point (usually `HEAD`) where it is not
* `git bisect` effectively puts you in a detached `HEAD` state and then binary searches through the commit difference
  * Detached `HEAD` is effectively an anonymous branch
* `git bisect bad <commit>` (will use `HEAD` by default)
* `git bisect good {commit}`
* From that point on just say `git bisect good / bad` to continue the process
* `bisect` keeps log of "answers" along with corresponding commit ids - `bisect log`
* `git bisect reset` to get back on original branch
* `git blame -L {line number}, {file path}` - to see history for a specific file & line
* `git log -S<string>` searches back through file diff history for the given string
  * If a file has the same number of instances of lines with phrase, they won't be shown since it won't be part of the _diff_
