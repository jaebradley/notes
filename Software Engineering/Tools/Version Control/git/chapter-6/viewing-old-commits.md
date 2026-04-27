# Viewing Old Commits

* `git log` acts like `git log HEAD` and prints commits reachable from `HEAD`
* `git log <commit>`, the log starts from named commit (or branch)
* You can `git log` using a range (`since..until`) like `git log master~12..master~10`
  * This will show commit between the 10th and 11th prior commits on the `master` branch
* `--pretty=<oneline|short|full>` provides formatting options
* `abbrev-commit` will just abbreviate commit hash
* `-p` will print the patch / changes introduced by the commit
* `--stat` provides data on the files changed in a commit and line modifications in each file
* `git show` to display objects from the object store
  * Can point to a specific commit like `git show HEAD~2`
  * Can point to a specific file like `git show origin/master:SOME_FILE` - this will show the blob `SOME_FILE` in the `origin/master` branch
