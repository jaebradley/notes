# Branches

* `git merge-base <original branch> <new branch>` can be used to find commit where branches diverged
* branch name is pointer to a specific (evolving) commit
* `git branch`
  * `-r` shows remote tracking branches
  * `-a` shows all branches

## `git show-branch`

* A commit is present in a branch if there is a `+`, `*`, or `-`
* `+` indicates commit is in branch
* `*` indicates commit is present on active branch
* `-` indicates merge commit
* If the same commit is present in multiple branches, it will have a `+` or `*` indicator
* `+ * +` means that commit is in first branch, the commit is in the active branch, and the commit is in the `master` branch
* To compare commit history of two branches `git show-branch branch1 branch2`

## Checking out

* Files and directories present in the branch being checked out but not in current branch are checked out of object store and placed into working tree
* Files and directories present in current branch bu absent in branch being checked out will be removed from working tree
* Files and directories that are not being tracked are always left alone

## Detached `HEAD`

* Git creates a detached `HEAD` when
  * Check out commit that is not tip of branch
  * Check out a tracking branch
  * Check out commit referenced by a tag
  * `git bisect`

## Deleting

* Git won't allow deletion of branch that contains commits that are not present on current branch
* If content is present on _another_ branch, checking that branch out and then requesting branch deletion would also work
  * Can also merge content from the branch you want to delete in current branch
* Once a branch name is deleted, it is gone forever
