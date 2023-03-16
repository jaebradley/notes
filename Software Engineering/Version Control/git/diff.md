# [Git Diff](https://jaimeiniesta.github.io/learn.github.com/p/diff.html)

* To see all the changes between working directory and last commit, directly compare them by specifying `HEAD` like `git diff HEAD`
* Will show both staged and unstaged changes
* How a file has changed since a specific point in history (like a tag)
  * `git diff v1.6.9 -- README` shows how the `README` file has changed since the `1.6` release tag
* Can also see the changes between two snapshots like `git diff v1.0 v1.1`
  * Can also pass `-stat` to see statistics for the files that are different
* Can also do `git diff v1 v2 -- some_file` to look at the differences for a given file between two versions
* `git diff master...dev` will will show what `dev` has changed since branches diverged
  * This will not compare the last `master` branch snapshot and the last `dev` snapshot
  * It will instead compare the common ancestor of both with `dev`
  * So will tell you what changed _since_ the branch point
  * If currently on `master`, `git diff ...dev` - will say what merging `dev` into `master` would introduce
