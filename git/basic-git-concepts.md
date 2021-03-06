# Basic Git Concepts

* [Git Object Types](#git-object-types)
* [Git Tracks Content](#git-tracks-content)
* [Git SHA1 Hash Function](#git-sha1-hash-function)
* [Tree Hierarchies](#tree-hierarchies)
* [Commits](#commits)

## Git Object Types

* Blobs
  * Each version of a file is represented as a blob.
* Trees
  * A tree represents one level of directory information. It records blob identifiers, pathnames, and other metadata for all files in one directory.
  * It can recursively reference other (sub)tree objects and thus build a complete hierarchy of files and subdirectories.
* Commits
  * A commit object holds metadata for each change introduced into the repository, like author, commiter, commit date, and log message.
  * Each commit points to a tree object that captures, in one complete snapshot, the state of the repository at the time the commit occurred.

## Git Tracks Content

* Git's object store is based on the hashed computation of the contents of its objects, not on the file or directory names from the user's original file layout.
* When Git places a files into the object store, it does so based on the hash of the data and not on the name of the file.
  * Git does not track file or directory names, which are associated with files in secondary ways.
* Git stores every version of every file - not their differences - as files go from one revision to the next.
  * Because Git uses the hash of a file's complete content as the name for that file, it must operate on each complete copy of the file.

## Git SHA1 Hash Function

* If you create the exact same content as another developer, regardless of where or when or how, an identical hash is proof enough that the full content is identical - in fact, Git treats them as identical
* Identical SHA1 hashes **do not count as a collision**. It would be a collision if two different objects produced the same hash - however, with SHA1 hashes, two instances of the same content produced the same hash
* This also means that if you have a tree with the SHA1 hash 1234, you can be confident that it's identical to anybody else that has a tree with the same hash.
  * Conversely, if you don't find a hash in your object store, you can be confident you don't hold a copy of that object
* Consider the most recent commit - since it contains, as part of its content, the hash of its parent commits, and of its tree, and since that in turn contains the hash of all of its subtrees and blobs, etc. etc. it follows that the hash of the original commit uniquely identifies the state of the whole data structure rooted at that commit.
  * This means we can compare two objects without transmitting either in full

## Tree Hierarchies

* If we create new subdirectory that contains an identical copy of hello.txt the subdirectory will have the same SHA1 hash as the parent directory because it's contents are identical
  * In git objects there will be three unique objects - blob containing "hello world", tree containing hello.txt, and a second tree that contains another reference to hello.txt

## Commits

* Commits contain your name and the time you made the commit, so even commits with the same tree are going to differ.
  * Different commits often refer to the same tree
    * When this happens, Git is smart enough to transfer around only the new commit object, which is much smaller than the tree and blob objects
* Commits consist of
  * The name of a tree object that actually identifies the associated files
  * The name of the person who composed the new version (the author) and the time it was authored
  * The name of the person who placed the new version into the repository (the committer) and the time it was committed
  * A description of the reason for this revision (commit message)
* Commits are stored in a graph structure
  * When you make a new commit, you can give it one or more parent commits that basically tell the history of your project
