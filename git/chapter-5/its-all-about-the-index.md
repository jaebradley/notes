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

## Example

* `master` branch with files `foo1` and `foo2` that are in the index, and object store
* `foo1` is edited with different content - index and object store have not changed, but working directory is not considered dirty
* After staging `foo1` through `git add`, `Git` takes the `foo1` from the working directory, computes a SHA1 hash, and puts the ID in object store
* `Git` then updates the index that `foo1` has been updated to new ID
* Working directory, index, and object store are all considered in-sync
* Index is considered dirty with respect to `HEAD` because the tree recorded in object store for the `HEAD` commit in the `master` branch is different
* Committing converts the virtual tree in index to a real tree object and puts that tree into the object store using the SHA1 hash
* New commit object is created with log message - this object points to the created tree object and to the previous commit
* `master` branch ref is moved from most recent commit to newly created commit object
