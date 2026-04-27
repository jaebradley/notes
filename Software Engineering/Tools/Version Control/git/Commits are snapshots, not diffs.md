# [Commits are snapshots, not diffs](https://github.blog/2020-12-17-commits-are-snapshots-not-diffs/)

* Git references objects by their object id
* Each object is a plain-text file that can be examined using the `git cat-file -p <oid>` command

## Blobs are file contents

* Blobs contain file contents
* Can find the object id for a file at a specific revision (like `HEAD`) by going `git rev-parse HEAD:<path>`
* `git cat-file -p <object id>` to look at its contents
* After editing the same file, if `git status` is executed, it notices that the file has a recent modified time and hashes the file's contents
  * If the contents don't match the current object id, then `git status` reports the file as modified on disk

## Trees are directory listings

* File names come from Git's representation of directories via trees
* A tree is an ordered list of path entries, paired with object types, file modes, and the object id for the object at that path
* Subdirectories are also represented as trees, so trees can point to other trees
* The tree referenced by `<ref>^{tree}` (like `HEAD^{tree}` is a special tree / the root tree

## Commits are snapshots

* A commit is a snapshot in time
* Each commit contains a pointer to its root tree representing the state of the working directory at the time
* A commit has a list of parent commits corresponding to previous snapshots
* A commit with multiple parents is a merge commit

## Branches are pointers

* A branch with name `main` is actually a reference in Git called `refs/heads/main`
* The file at `.git/refs/heads/<some branch>` contains the hex string referencing the object id of a commit
* Commits, trees, and blobs are immutable
* `HEAD` is a reference that points to the current branch

## Computing diffs

* To compare commits, start looking at their root trees, which are almost always different
* Perform DFS on the subtrees by following pairs when paths for the current tree have different object ids
* If there's a `docs` directory that exists in both commits but has a different object id, recursively diff that `docs` directory
* If the `docs` directory tree has different object ids for a specific blob, that blob is compared line-by-line
* Obviously ignore directories and files that have the same object ids
* The cost of coomputing a diff is relative to the number of paths with different content

## What does `git cherry-pick` do

* Compute diff between the commit id and its parent
* Apply that diff to the current `HEAD`
* Create a new commit whose root tree matches the new working directory and whose parent is the commit at `HEAD`
* Move the ref at `HEAD` to that new commit
* Didn't "move" the commit to be on top of our current `HEAD`, a new commit was created whose diff matches the old commit

## What does `git rebase` do

* Discovers the list of commits that are reachable from `HEAD` but not reachable from `target` (`git log --oneline <target>..HEAD`)
* Starting from `<target>` will perform `git cherry-pick` commands on this commit range, starting from the oldest commits
* At the end, have a new set of commits, with different object ids, but similar diffs to the original commit range

## How does Git track renames

* There is no data structure inside Git that stores a record that a rename happened between a commit and its parent
* Exact and edit rename detection
* After computing a diff, Git inspects a diff to see which paths were added or deleted
* If a file was moved, it would appear as a deletion from the first location and an addition in the second location
* To detect files that were renamed and edited, Git iterates through each added file and compares that file against each deleted file to compute a similarity score as a percentage of lines in common
  * Unfortunately, this is quadratic (number of additions x number of deletes) so Git will avoid renames if the number of additions and deletions is larger than some internal limit
