# [Git Commands To Live By](https://medium.com/better-programming/git-commands-to-live-by-349ab1fe3139)

* Check out remote branches
  * `git checkout -b some-local-name -t remote/branch`
    * Don't need `-b` option
* Delete remote branch
  * `git push some-remote -d some-branch` or `git push some-remote :some-branch`
* Stash individual files
  * `git stash push -- some file.paths`
* Show content of most recent stash
  * `git stash show -p`
  * The `-p` shows the content of the stash - omitting it will show only the file names
* Compare files between branches / commits
  * `git diff some-commit-or-branch some-commit-or-branch -- some file.paths`
* Unstage a file
  * Reverses `git add`
  * `git reset HEAD some.path`
  * Removes the given file from the index (but doesn't affect anything about the file itself)
* Remove ignored files
  * `git rm -r -n --cached some file.paths`
    * `--cached` ensures files exist in working tree
    * `-n` performs a dry run
    * `-r` is used when folders are being removed, so that recursive removal is allowed
