# [The elements of git](https://cuddly-octo-palm-tree.com/posts/2021-09-19-git-elements/)

## Objects and refs

* A git repo has two subdirectories - the `objects` subdirectory and the `refs` subdirectory
* `objects` directory stores objects named after the `SHA-1` hash of its content
* objects are immutable - the "same" object with new content will generate a different `SHA-1` hash value, and thus have a different name / will be a new object
* A "ref" in the `refs` subdirectory is a text file whose contents are exactly one `SHA-1` hash value, which refers to the git object named by the `SHA-1` hash

### `blob` objects

* `blob` objects are a stream of bytes - these represent the content of the files in the git working tree

### `tree` objects

* `tree` objects represent a directory
* Each line in the tree object text file represents an entry in the directory
* Entries can be a `tree` object themselves (nested directory) or a `blob` object (file)
* A file may be stored at the path `.git/objects/01/{HASH}` where the file contents are a compressed representation of an encoding of the content
* Trees do not know their own name - a tree's name is determined by its parent
  * This is beneficial if a folder name changes, because git needs to rewrite the parent tree object entry for the updated subdirectory, but can still leave the tree, and all of its content unchanged

### `commit` objects

* Text files with various data about the work being committed
* Text file contains values like `tree`, `parent`, `author`, `committer`
 * `tree` references a `tree` object that will serve as the root of the worktree for the commit
 * `parent` header represents the set of parents for this commit (most commits will have 1 but merge commits may have many)

### `tag` objects

* Can point to any type of object, but most often point to commits

### `branch` ref

* Text files containing a `SHA-1` that represents a branch
* Points to a commit
* Files are located at `.git/refs/heads` or `.git/refs/remotes`
* The name of the branch is the name of the file in either location

## How this all fits together

* `HEAD` is a symbolic ref to `refs/heads/main`
* `refs/heads/main` has a direct ref to current commit `C0`
* commit `C0` points to tree `T0` and to parent `C1`
* parent commit `C1` points to tree `T1` and more parents
* Tree `T0` points to blobs `B0` and `B1` and subdirectory `T2`
* Tree `T1` points to blobs `B0`, and `B2`, and subdirectory `T2` (`B2` is the file that's changed between `C0` and `C1`)
* git does not store diffs and computes diffs at run time
* `git diff` performance is a function of how different their content is
* Reusing trees makes for very efficient diffs as the diffing algorithm knows it does not need to descend into common trees at all

## `git packs`

* A pack is a compressed representation, which means that for fairly transparent file formats like plain text, it is likely the compression algorithm can take care of not repeating long chunks of identical text too many times

## `git garbage`

* When you create a new commit, it points to its parent, which means you can't throw away the parent
 * Creating new commits, including merge commits, is a purely additive operation
* Some operations are destructive (`rebase`, `reset`, `commit --amend`) that ignore part of the existing history
 * Can end up with orphan git objects that cannot be reached from `HEAD` or any of the `branches` or `tag` refs
 * `git gc` will scan the object files and packs to find these orphan objects and remove them

## Why git does not work with large binary files

* Every change is stored as a separate file
* Binary files do not typically compress well
* git also assumes it's working with small files and will have behavior (like trying to load blobs into memory when diffing) that assumes that files are small
