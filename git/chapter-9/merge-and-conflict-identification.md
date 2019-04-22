# Merge and Conflit Identification

* During normal merge, Git creates new versions of files and places them in working directory post-merge
* Git also uses the index to store temporary and intermediate versions of files during merge
* `git status` or `git ls-files -u` will show files that are unmerged in working tree
* `git diff` is a combination of `git diff HEAD` and `git diff MERGE_HEAD`
  * `HEAD` is the first parent and `MERGE_HEAD` is the second parent
  * `git diff --ours` is equivalent to `git diff HEAD`
  * `git diff --theirs` is equivalent to `git diff MERGE_HEAD`
* Conflict symbols
  * `+` indicates an added line
  * `-` indicates a removed line
  * `++` indicates a conflict
  * First column indicates what has changed vs. your version
  * Second column indicates what has changed vs. other version
* `git log --merge --left-right -p`
  * `--merge` - only shows commits related to files that produced a conflict
  * `--left-right` displays `<` if commit was from "left" side of merge ("our" version)
    * "right" side is "their" version, the one you're merging in
  * `-p` shows the commit message and patch associated with the change
