# [Creating a Git commit: The Hard Way](https://avestura.dev/blog/creating-a-git-commit-the-hard-way)

* Staging area is an index file inside the `.git` directory that holds the information about what will go into your next commit
  * Staged files live in this file

## Git Objects: Commits, Trees, and Blobs

* git commit is a type of an object in git
  * Other git object types are `blob`, `tree`
  * Can be found inside `.git/objects`

### Blob Objects

* Calling `git hash-object` does the following
  * The hashed object file name is the SHA-1 hash of the blob content
  * The blob content is `blob`, followed by a space, followed by the content bytesize, followed by `\0` followed by the content

### Tree Objects

* Tree is a collection of references to blobs or other trees
* A tree object file will have references to the child object type (`blob` or `tree`), the SHA-1 hash for that object, and the name of that object (file name or directory name)

### Commit Objects

* Commit objects contain a refernce to a Git tree, the commit author, commit timestamp, commit message
* Commit can have zero parents (initial commit), one parent, or multiple parents (merge commit)

## Craft a Commit, the hard way

* `git update-index` to update the staging area / index file
  * `--add` adds the file to the index, as it isn't already there
  * `--cacheinfo <file mode> <object hash> <file path>`
    * `100644` file mode number means a normal file - different file modes for executable files, symbolic links, etc
* Create a tree object using `write-tree`
* `git cat-file -p` to check the content of the created tree object (and any object file)
* `echo 'Some commit message' | git commit-tree <tree hash>` creates a commit object referencing the created tree
  * `-p <parent commit hash>` to create a commit referencing a parent
