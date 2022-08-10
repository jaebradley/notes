# [The elements of git](https://cuddly-octo-palm-tree.com/posts/2021-09-19-git-elements/)

## Objects and regs

* A git repo has two subdirectories - the `objects` subdirectory and the `refs` subdirectory
* `objects` directory stores objects named after the `SHA-1` hash of its content
* objects are immutable - the "same" object with new content will generate a different `SHA-1` hash value, and thus have a different name / will be a new object
* A "ref" in the `refs` subdirectory is a text file whose contents are exactly one `SHA-1` hash value, which refers to the git object named by the `SHA-1` hash
* `blob` objects are a stream of bytes - these represent the content of the files in the git working tree
* `tree` objects represent a directory
  * Each line in the tree object text file represents an entry in the directory
  * Entries can be a `tree` object themselves (nested directory) or a `blob` object (file)
  * A file may be stored at the path `.git/objects/01/{HASH}` where the file contents are a compressed representation of an encoding of the content
  * Trees do not know their own name - a tree's name is determined by its parent
    * This is beneficial if a folder name changes, because git needs to rewrite the parent tree object entry for the updated subdirectory, but can still leave the tree, and all of its content unchanged
