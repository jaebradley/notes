# Conflicts 

* Git keeps track of the SHA1 of the commit you're merging in, in `.git/MERGE_HEAD`
* Git keeps track of the default merge message in `.git/MERGE_MSG` after commiting post conflict resolution
* Git keeps track of three copies of conflicted file
  * the merge base version - stage number `1`
  * "our" version - stage number `2`
  * "their" version - stage number `3`
  * `git ls-files -s` will show all files in _all_ stages
  * `git diff 1:hello 3:hello` will show difference between merge base and version you're merging in
  * Stage number of `0` indicates an unconflicted file
`git log` and `git show` will always print the SHA1 of an ancestor
* The diff of a merge commit is not a normal diff
  * Since a merge is simply changes from other parts of history that have been combined together, the diff of a merge commit is differences from one of the merged branches
* To recover after a botched conflict, `git checkout -m`

