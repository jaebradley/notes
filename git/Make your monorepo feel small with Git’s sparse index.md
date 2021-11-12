# [Make your monorepo feel small with Git’s sparse index](https://github.blog/2021-11-10-make-your-monorepo-feel-small-with-gits-sparse-index/)

* The git index stores filesystem information about your working directory
  * The index stores this information for every file at `HEAD` regardless of if the files are outside the `sparse-checkout` definition
  * The index can be much larger in a monorepo than it would be if your important subset of files was in its own repository
* `sparse-index` focuses on files within the `sparse-checkout` cone
  * The sparse index will scale with the number of files within the chosen directories

## The Git Index

* The index file stores a list of every file at `HEAD` along with the object ID for its blob and some metadata
  * `git ls-files` will expose list of files in the index
  * `sparse-checkout` by itself will not return a different set of files when the `git ls-files` command is executed
* The `--debug` argument to the `ls-files` command returns additional information for each index entry
  * For a file outside the `sparse-checkout` definition, the filesystem information is removed and the `SKIP_WORKTREE` bit is set to signify that Git will not write that file to the working directory
* The files outside the `sparse-checkout` definition are listed in the index because Git still needs to understand the content that would be there
  * The index information is necessary to generate a commit with the `git commit` command
* While the blobs in the index are a flat list, in reality the blobs form a tree
* The cache-tree extension stores a list of sub-nodes and a range of index entries that are covered by the current node
  * The root node always covers all of the blobs in the index
  * In other words, each node covers all the blobs in its
* `git add` updates the cache-tree extension in order to make the next `git commit` command very fast
* `git status` command reads index from disk and parses it into memory
  * Then the working directory is comapred against the index
  * The updated index is written to disk


## The sparse index

* It can store directory paths with the object ID for its tree object
* Since the spare-checkout patterns match on a directory level, can determine if an entire directory is out of the sparse-checkout cone and replace all of its contained file paths with a single directory path
  * In other words, all the files in the directories outside the spare-checkout definition are represented as a single file entry
  * So `git ls-files --debug` would show the directories outside the sparse-checkout definition but not any sub-directories or files. These directories would have their filesystem information removed
