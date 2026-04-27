# Reset, Revert and Checkout

* `git reset` does not change your branch
  * If provided a branch name it will change the `HEAD` of the current branch to be the tip of the named branch
  * `git reset --hard` is intended to be used as a recovery mechanism to a previous known state
    * It is also used to clear out failed or stale merge efforts
    * Using `git checkout` instead of `git reset --hard` during a pending merge will result in a merge commit
* `git checkout` is often confused with "resetting" a file because it moves a file from object store
  * The proper way to conceptualize this operation is that a given file has been "checked out" from a given commit
* `git revert` operates on commits and not files
* If other developers are cloning a repository, you should probably not use commands that alter history (i.e. prefer `git revert` over `git reset --hard`)

 
