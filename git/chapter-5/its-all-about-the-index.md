# It's All About The Index

* `Git`'s index does not contain any file content - it simply tracks what you want to commit
* When `git commit` is executed, `Git` checks the index rather than the working directory to discover what to commit
* `git status` reveals state of the index
* `git diff` reveals tracked files with changes that have not been staged
* `git diff --cached` reveals what is staged

## File Classification

* Tracked - these are files that have been staged in `Git` (at some point in time)
* Ignored - there are files that have been ignored by `Git` in some way (for example, via a `.gitignore`)
* Untracked - there are files that are not Tracked nor Ignored

## What does `git add` do

* If there are unstaged changes, executing `git add` will copy the file into `Git`'s object store and hash it
* Thus, each subsequent edit (and `git add` should result in a different hashed version of the file in `Git`)
* While files have been added to the object store, the index merely references these files
* `Git` uses staged changes for committing
