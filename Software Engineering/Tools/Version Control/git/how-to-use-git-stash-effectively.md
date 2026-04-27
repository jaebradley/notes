# [How To Use `git stash` Effectively](https://levelup.gitconnected.com/how-to-use-git-stash-effectively-d4e04870cc2b)

* List all git stashes - `git stash list`
  * Each stash should have a name, the working directory and the `HEAD` commit
  * Most recent stash will be at top (stash works like a stack)
* `git stash show` will show the changes in the latest stash
  * Can specify the changes in a particular stash by looking at it via `git stash show stash@{2}`
  * Can show file changes of a diff by using the `-p` flag
* Can apply stashed changes by using the `apply` command - by default it will apply the latest stash (i.e. `stash@{0}`)
* `git stash drop` will delete the latest stash - can also specify a specific stash
* `git stash branch <some branch name> stash@{5}` will create a branch from a specific stash
